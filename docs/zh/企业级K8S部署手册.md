# 企业级 K8S 部署手册

本文面向需要在 Kubernetes 集群中部署 JiuwenSwarm 的企业用户，说明部署前准备、基础资源规划、部署流程、启动验证和常见问题排查。

> 说明：本文先提供企业级部署手册的基础框架，后续可结合实际 Helm Chart、Kustomize 或 YAML 模板继续补充。

---

## 1. 部署前准备

在部署 JiuwenSwarm 前，请先确认以下条件：

| 项目 | 要求 | 说明 |
|------|------|------|
| Kubernetes | 1.24+ | 建议使用企业内部稳定版本 |
| kubectl | 与集群版本兼容 | 用于执行部署和排查命令 |
| 镜像仓库 | 可拉取 JiuwenSwarm 相关镜像 | 建议使用企业内部镜像仓库 |
| Ingress / Gateway | 已完成基础配置 | 用于暴露 Web 或 API 服务 |
| 存储 | 按需配置 PVC | 用于持久化配置、日志、任务和记忆数据 |
| 模型服务 | 已准备可访问的大模型服务 | 可使用 OpenAI 兼容接口或企业内模型服务 |

---

## 2. 推荐资源规划

企业部署建议将 JiuwenSwarm 独立放入一个 namespace：

```bash
kubectl create namespace jiuwenswarm
```

推荐资源拆分如下：

| 资源类型 | 作用 |
|----------|------|
| ConfigMap | 保存非敏感运行配置 |
| Secret | 保存模型密钥、认证信息等敏感配置 |
| Deployment | 部署 Web、后端服务、AgentServer、Gateway 等组件 |
| Service | 提供集群内服务发现 |
| Ingress | 提供外部访问入口 |
| PVC | 持久化任务、日志、记忆、配置等运行数据 |

---

## 3. 配置项检查

部署前建议先确认以下配置：

1. **模型配置**：模型服务地址、模型名称、接口类型、鉴权方式。
2. **运行模式**：单机模式、AgentServer 模式或企业 Gateway 模式。
3. **存储路径**：配置、日志、技能、记忆、定时任务等数据是否需要持久化。
4. **访问入口**：Web 访问域名、API 访问路径、Ingress 规则。
5. **权限策略**：工具调用、文件访问、命令执行等权限控制是否开启。
6. **频道配置**：是否接入 Web、飞书、企业微信、钉钉等频道。

---

## 4. 部署流程

### 4.1 创建命名空间

```bash
kubectl create namespace jiuwenswarm
```

### 4.2 创建配置资源

建议将普通配置放入 ConfigMap，将敏感配置放入 Secret。实际字段以项目运行配置为准。

```bash
kubectl apply -f configmap.yaml -n jiuwenswarm
kubectl apply -f secret.yaml -n jiuwenswarm
```

### 4.3 部署服务组件

按以下顺序部署：

1. 后端服务。
2. Web 前端服务。
3. AgentServer 或 Worker 组件。
4. Gateway 或 Ingress 入口。

```bash
kubectl apply -f backend.yaml -n jiuwenswarm
kubectl apply -f web.yaml -n jiuwenswarm
kubectl apply -f agentserver.yaml -n jiuwenswarm
kubectl apply -f ingress.yaml -n jiuwenswarm
```

---

## 5. 启动后验证

部署完成后，执行以下命令检查服务状态：

```bash
kubectl get pods -n jiuwenswarm
kubectl get svc -n jiuwenswarm
kubectl get ingress -n jiuwenswarm
```

建议按以下顺序验证：

1. Pod 状态为 `Running`，Ready 状态正常。
2. Web 页面可以正常访问。
3. 模型配置可以保存并通过连通性验证。
4. 可以完成一次基础对话。
5. 可以创建并执行一次简单任务。
6. 如果启用 Swarm / AgentServer，确认多智能体任务可正常流转。

---

## 6. 日志排查

查看后端日志：

```bash
kubectl logs -n jiuwenswarm deploy/<backend-deployment-name>
```

查看 AgentServer 日志：

```bash
kubectl logs -n jiuwenswarm deploy/<agentserver-deployment-name>
```

查看最近事件：

```bash
kubectl get events -n jiuwenswarm --sort-by=.lastTimestamp
```

---

## 7. 常见问题

| 问题 | 可能原因 | 排查方向 |
|------|----------|----------|
| Web 页面无法访问 | Ingress、Service 或前端配置异常 | 检查域名、端口、Service 和 Ingress |
| 模型调用失败 | 模型地址或鉴权配置异常 | 检查模型配置和网络连通性 |
| 任务无法执行 | AgentServer / Worker 未启动 | 检查相关 Pod 和日志 |
| 定时任务未触发 | 调度服务未运行或时区配置不一致 | 检查定时任务配置、日志和 timezone |
| 权限规则未生效 | 策略未加载或执行链路未校验 | 检查权限配置和工具调用日志 |

---

## 8. 后续补充方向

- 补充官方 Helm Chart 或 Kustomize 示例。
- 补充生产环境资源 requests / limits 建议。
- 补充多副本、反亲和、滚动升级和回滚策略。
- 补充企业 Gateway、AgentServer 高可用部署拓扑。
- 补充 Prometheus、日志采集和告警配置。