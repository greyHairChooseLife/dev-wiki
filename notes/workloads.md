---
hide:
title: 'Workloads'
date: 2024-04-05
update:
---

> Resource란 쿠버네티스 오브젝트의 타입을 칭한다.  
> 즉, 쿠버네티스 오브젝트는 Resource의 인스턴스이다.

## 들어가며

Resources는 다음과 같은 구성요소로 이루어져 있다.

- Deployment, Service, Ingress, ConfigMap, Secret, PersistentVolume, PersistentVolumeClaim, StorageClass,
  Namespace 등이 있다.

## Pod

1. `docker run` 으로 컨테이너를 생성/실행한 것 처럼 `kubectl run` 명령으로 pod를 생성/실행할 수 있다.

   다만 쿠버네티스 생태계에서 pod처럼 작은 단위를 직접 관리하지는 않는것이 보통이다. 대신 Deployment나
   StatefulSet 같은 리소스를 사용한다.

2. pod는 하나 이상의 컨테이너로 구성된다. pod 자체의 상태를 조회할 수도 있고, pod 내부의 특정 컨테이너를
   조회할 수도 있다.

   ```sh
   kubectl get pod
   kubectl get pod -o wide

   # 주로 Events부분을 확인한다. Pod의 생성, 시작, 종료 등의 이벤트를 순서대로 확인할 수 있다.
   kubectl describe pod/<podname>

   kubectl logs pod
   kubectl logs -f pod # log streaming, --follow

   kubectl exec -it <podname> -- sh
   kubectl exec -it <podname> -c <container name> -- sh
   ```

## ReplicaSet

- pod의 템플릿을 정의하고, 원하는 수의 pod 인스턴스를 유지하도록 한다.

  ```sh
    spec:
        replicas: <pod 복제본 수량>,
        selector: <pod 선택자, 무엇을 관리할 것인지 알아볼 수 있도록 지정한다.>,
        template: <복제 생성할 pod 템플릿>
  ```

## Deployment

- 가장 널리 사용되는 오브젝트다.
- ReplicaSet을 이용(스스로 생성)하여 Pod를 관리한다. Pod의 업데이트, 롤백, 스케일링 할 수 있다.
- 버전 업그레이드는 너무나 쉽다. 컨테이너로 제품을 배포하는 만큼 이미지 기반으로 버전을 관리할텐데,
  레지스트리에서 업데이트된 이미지를 설정하고 다시 apply 해 주면 땡이다.

  ```sh
  piVersion: apps/v1
  kind: Deployment
  metadata:
    name: hello-world
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: hello-world
        tier: production
    template:
      metadata:
        labels:
          app: hello-world
          tier: production
      spec:
        containers:
          - name: hello-world
            # 이렇게 새로운 이미지로 설정한다.
            image: my.registry/hello-world:v1 ==> my.registry/hello-world:v2
  ```

- 버전 변경 히스토리를 확인하고, 롤백도 쉽다.

  ```sh
  kubectl rollout history deployment/hello-world
  kubectl rollout undo deployment/hello-world

  # revision을 지정하면 상세 정보를 보거나 해당 위치로 롤백 가능하다.
  # 이때, delployment resource의 spec.revisionHistoryLimit 값으로 설정된 만큼만 revision이 유지된다.
  # commit history처럼 모두 유지되면 편리하겠지만, 그만큼 **리소스를 차지**하므로 적절히 제한해야 한다.
  ```

- 배포 전략을 설정할 수 있다. 기본값은 rolling update인데, `Pod` 기본적으로 25%씩 업데이트 해 간다. 이 값을
  조절할 수 있다.

  ```sh
  # deployment resource의 spec.strategy를 설정한다.
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxSurge: 3 # 최대로 생성할 pod 수
        maxUnavailable: 3 # 최대로 사용할 수 없는 pod 수
  ```
