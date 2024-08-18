---
dhide:
title: 'Services'
date: 2024-04-05
update:
---

## 들어가며

**service**는 쿠버네티스 클러스터의 네트워크를 담당한다.

## `ClusterIP`

### why we need it?

`ClusterIP`는 클러스터 내부 통신에 활용된다. `Pod`와 다른 `Pod`끼리 통신하는 것 말이다.

그런데 어떤 `Pod`와 그 복제본들이 생성되고 삭제 될 때마다 그것들의 IP는 달라진다. 그래서 이들을 대표할
추상화된 네트워크가 필요한 것이다.

### ClusterIP가 생성된 이후의 (자동화된)과정

- `Endpoint Controller`는 `Service`와 `Pod`을 감시하면서 조건에 맞는 `Pod`의 `IP`를 수집
- `Endpoint Controller`가 수집한 `IP`를 가지고 `Endpoint` 생성
- `Kube-Proxy`는 `Endpoint` 변화를 감시하고 노드의 `iptables`을 설정
- `CoreDNS`는 `Service`를 감시하고 서비스 이름과 `IP`를 `CoreDNS`에 추가
  - CoreDNS: 쿠버네티스 클러스터 내부에서 DNS 서비스를 제공한다. service의 name만으로 통신 가능하게 해준다.
    클러스터 호환성을 위해 `kube-dns`라는 이름으로 생성된다.

### yml파일 예제

<details>
  <summary>두개의 Worker Nodes 연결하기</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: redis
spec:
selector:
  matchLabels:
    app: counter
    tier: db
template:
  metadata:
    labels:
      app: counter
      tier: db
  spec:
    containers:
      - name: redis
        image: redis
        ports:
          - containerPort: 6379
            protocol: TCP

# 구분선을 통해 여러 Resource를 생성할 수 있다.
# 하나의 파일 안에있는 여러 Resource가 서로 연관되어야 하는 것은 아니다.
---
apiVersion: v1
kind: Service
metadata:
name: redis
spec:
# type이 비었을 경우 기본값은 ClusterIP
ports:
  - port: 6379
    protocol: TCP
selector:
  app: counter
  tier: db

---
apiVersion: apps/v1
kind: Deployment
metadata:
name: counter
spec:
selector:
  matchLabels:
    app: counter
    tier: app
template:
  metadata:
    labels:
      app: counter
      tier: app
  spec:
    containers:
      - name: counter
        image: ghcr.io/subicura/counter:latest
        env:
          - name: REDIS_HOST
            value: 'redis' # Service의 이름으로 통신할 수 있다. CoreDNS가 Service 이름을 IP로 변환해준다.
          - name: REDIS_PORT
            value: '6379'
```

</details>

## `NodePort`

### why we need it?

`Pod`를 외부로 노출시키고 싶을 땐 `NodePort`를 사용할 수 있다.

> `ClusterIP`가 없어도 `NodePort`는 `pod`에 연결될 수 있다. 어차피 Node의 bridge(cbr0)에 네트워크 정보가
> 있다.
>
> 하지만 이를 동시에 사용할 때 **세션 지속성**을 활용하여 어떤 `요청`을 (여러 복제된 `pod` 중에서)이전
> 요청을 수행한 `pod`로 다시 보낼 수 있다.
>
> 반면에 `NodePort`는 세션 지속성을 제공하지 않는다. 그냥 분산시킨다.

### yml파일 예제

<details>
<summary>펼치기</summary>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: counter-np
spec:
  type: NodePort # Service Resource의 타입을 지정
  ports:
    - port: 3000 # 외부에 노출될 포트
      protocol: TCP
      nodePort: 31000 # 목적지 포트, 30000~32767 사이의 포트를 사용할 수 있고, 지정하지 않으면 이 범위에서 랜덤으로 할당된다.
  selector: # 어떤 pod를 노출시킬지 선택
    app: counter
    tier: app
```

</details>

## `LoadBalancer`

클라우드 서비스에서 제공하는 로드밸런서를 사용할 수 있다. 이는 클라우드 서비스에 따라 다르다.

on-premise 환경에서는 기능이 제한적이다. 이때는 [MetalLB](https://github.com/metallb/metallb)를 사용할 수
있다.

## `Ingress`

여타 controller나 service와 달리 이건 따로 설치해야한다. 선택지가 많고 입맛대로 고르면 되니까 그런가보다.

    - nginx
    - traefik
    - haproxy
    - alb
    - ...

설치하면 설정파일 없이 nginx를 실행한듯 동작한다. 이 `conf.d`의 역할을 이제 `ingress spec`가 대체한다.
