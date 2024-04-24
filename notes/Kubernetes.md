---
hide:
title: '쿠버네티스의 이해'
date: 2024-04-01
update:
---

> (개발 단계부터 이미지 생성까지는 도커 클라이언트를 활용하고)  
> 컨테이너 관련 작업이 배포 및 운영 단계로 넘어가기 시작하면 쿠버네티스를 사용할 때다.

## 목적

컨테이너화된 애플리케이션의 배포, 스케일링, 상태관리를 자동화

## 주요 구성요소와 용어

### Master Node(control plane)

- API 서버
- 스케줄러
- 컨트롤러 매니저
- etcd

### Worker Node

- kubelet
- kube-proxy
- 컨테이너 런타임
- Pods

### WorkerLoad

- 쿠버네티스 내에서 애플리케이션을 실행하는 다양한 방식, `Resources`의 일부 요소들
  - `Development`(`ReplicaSet`)
  - `DaemonSet`
  - `StatefulSet`
  - `CronJob`(`Job`)

### Resources

- 쿠버네티스 오브젝트의 타입을 칭한다.
  - `**Workload**(Pod, ReplicaSet, Deployment, ...)`
  - `**Service**`
  - `**Ingress**`
  - `ConfigMap`
  - `Secret`
  - `PersistentVolume`
  - `PersistentVolumeClaim`
  - `StorageClass`
  - `Namespace`
  - 등

### Objects

- **Resource**의 인스턴스

## reference

- [Kubernetes Explained in 6 Minutes | k8s Architecture](https://www.youtube.com/watch?v=TlHvYWVUZyc)
- [쿠버네티스 아키텍쳐-유투브](https://www.youtube.com/watch?v=-gIyfII5eak)
- [쿠버네티스 초급 안내서](https://subicura.com/k8s/guide/pod.html#%E1%84%88%E1%85%A1%E1%84%85%E1%85%B3%E1%84%80%E1%85%A6-pod-%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5)
- [따배-유투브](https://www.youtube.com/watch?v=lheclzO-G7k&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c&index=4)

# Installing

- [소속 개념](소속 개념)
- [step-by-step](step-by-step)

# Networking

- [temp-note-networking](temp-note-networking)

# Resources

- [api group](api group)
- [workloads](workloads)
- [services](services)
- [configMap](configMap)
- [Volume](Volume)(local)

