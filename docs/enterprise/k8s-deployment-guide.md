# Enterprise K8S Deployment Guide

> Status: MVP draft for issue #1594. This guide documents the enterprise deployment path and should be verified against the actual enterprise deployment assets before release.

## 1. Scope

This document describes how to deploy JiuwenSwarm in a Kubernetes environment for enterprise scenarios. It focuses on repeatable deployment, configuration isolation, service exposure, health checks, and operational verification.

## 2. Prerequisites

- A Kubernetes cluster with namespace creation permission.
- Container images for JiuwenSwarm backend, frontend, and related services.
- A configured ingress controller or equivalent gateway.
- Persistent storage if runtime state, logs, memory, or task data need to be retained.
- Access to at least one model provider supported by JiuwenSwarm, such as OpenAI-compatible APIs, Huawei Cloud MaaS, DeepSeek, DashScope, SiliconFlow, OpenRouter, or local models.
- A platform-approved way to manage sensitive runtime settings.

## 3. Recommended Namespace Layout

```bash
kubectl create namespace jiuwenswarm
```

Recommended resource groups:

| Resource | Purpose |
|---|---|
| ConfigMap | Non-sensitive runtime configuration |
| Deployment | Frontend, backend, worker, gateway |
| Service | Stable service discovery |
| Ingress | External access |
| PVC | Persistent runtime data when required |

## 4. Configuration Checklist

Before deployment, confirm:

- Model provider and base URL.
- Runtime mode: single-node, distributed swarm, or enterprise gateway mode.
- Storage path for memory, skills, task records, and logs.
- Tool access policy and file access boundary.
- Allowed callback or webhook domains if IM or external integrations are enabled.

## 5. Deployment Flow

1. Create namespace.
2. Apply configuration resources.
3. Deploy backend service.
4. Deploy frontend service.
5. Deploy worker or AgentServer components if enabled.
6. Deploy gateway or ingress.
7. Verify health endpoints.
8. Open the frontend and complete model configuration.
9. Run a simple conversation test.
10. Run a Swarm mode task to verify multi-agent orchestration.

## 6. Health Check

Minimum checks:

```bash
kubectl get pods -n jiuwenswarm
kubectl get svc -n jiuwenswarm
kubectl logs -n jiuwenswarm deploy/<backend-deployment-name>
```

Expected result:

- Pods are Running or Ready.
- Frontend can access backend.
- Model provider connection is successful.
- A basic conversation returns normally.
- Swarm mode can create and execute a task plan.

## 7. Rollback

Use the platform-native rollback method:

```bash
kubectl rollout history deployment/<name> -n jiuwenswarm
kubectl rollout undo deployment/<name> -n jiuwenswarm
```

Before rollback, save logs and relevant configuration for diagnosis.

## 8. Common Issues

| Symptom | Possible Cause | Suggested Check |
|---|---|---|
| Frontend opens but cannot chat | Backend URL or gateway route is wrong | Check frontend env and ingress route |
| Model call fails | Provider or base URL is invalid | Check provider configuration |
| Swarm task does not start | Worker or AgentServer is not ready | Check worker pods and logs |
| Scheduled task does not execute | Scheduler service or time configuration is missing | Check scheduler logs and timezone |
| File rule does not work | Access policy is not loaded or not enforced at backend | Check policy config and tool execution path |

## 9. TODO

- Add verified Helm or Kustomize examples.
- Add production resource requests and limits.
- Add sample ingress configuration.
- Add observability examples for logs, metrics, and alerts.
- Add HA deployment topology after enterprise architecture is finalized.
