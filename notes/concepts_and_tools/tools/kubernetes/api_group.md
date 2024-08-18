---
hide:
title: '쿠버네티스 리소스와 API 그룹'
date: 2024-04-09
update:
---

## Core Group (또는 "" 그룹)

- **Pods**
- **Services**
  - ClusterIP
  - NodePort
  - LoadBalancer
- **ConfigMaps**
- **Secrets**
- **Namespaces**

## apps/v1

- **Deployments**
- **ReplicaSets**
- **StatefulSets**
- **DaemonSets**

## extensions/v1beta1 (사용이 점점 줄어들고 있음)

- **Ingresses**

## rbac.authorization.k8s.io/v1

- **Roles**
- **RoleBindings**
- **ClusterRoles**
- **ClusterRoleBindings**

## networking.k8s.io/v1

- **NetworkPolicies**
- **Ingresses** (extensions에서 이동)

## batch/v1

- **Jobs**
- **CronJobs**

## storage.k8s.io/v1

- **StorageClasses**
- **PersistentVolumeClaims**
- **VolumeAttachments**

## autoscaling/v1

- **HorizontalPodAutoscalers**

## apiextensions.k8s.io/v1

- **CustomResourceDefinitions** (CRDs)

이와 같이, 쿠버네티스의 다양한 리소스는 서로 다른 API 그룹에 속해 있다. 이를 통해 쿠버네티스는 확장성과
유연성을 제공하며, 개발자는 필요에 따라 적절한 리소스와 API를 사용하여 애플리케이션을 구성할 수 있다.

> 유용한 커맨드 1: `kubectl api-resources` 유용한 커맨드 2: `watch -n 1 kubectl get cm,po,svc,ep,ing`
