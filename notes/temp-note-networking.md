---
hide:
title: 'Kubernetes networking'
date: 2024-04-07
update:
---

## Five essential things

1. 동일한 `Pod` 내의 `Containers` 간의 통신
2. 동일한 `node` 내의 `Pods` 간의 통신
3. 서로 다른 `node`에 속한 `Pods` 간의 통신
4. `Pod`와 `Service` 사이의 통신
5. `DNS`의 작동원리(새로운 `endpoint`를 어떻게 발견하는가?)

## TL;DR

<details>
<summary>펼치기</summary>

### 동일한 `Pod` 내의 `Containers` 간의 통신

- **통신 방법**: `localhost`와 `port`를 사용한다. 동일한 `Pod` 내의 컨테이너들은 같은 `network namespace`를
  공유하기 때문에, `localhost`를 통해 서로 통신할 수 있다.

### 동일한 `node` 내의 `Pods` 간의 통신

- Kubernetes 환경에서 각 `Pod`는 고유한 IP 주소를 가지며, 이는 해당 `node`의 `virtual ethernet device`에
  의해 관리된다. 각 `Pod`에서 발생하는 트래픽은 노드의 네트워크 브리지(`cbr0`)를 통해 라우팅되어, 해당 노드
  내 다른 `Pods`로 전달된다.

- 이때 (재)생성 되는 `Pod`마다 IP가 새롭게 생성된다. 이를 매번 쫓아다닐 수 없어서 추상화된 IP 하나를 만들어
  사용하는데, 이것이 바로 `service.clusterIP`다.

### 서로 다른 `node`에 속한 `Pods` 간의 통신

- 다른 노드에 있는 `Pod`로 트래픽을 보내려면, 트래픽은 먼저 송신 `Pod`가 속한 노드의 브리지를 거친 후,
  클러스터 내 라우팅 규칙을 통해 목적지 `Pod`가 있는 노드로 전달됩니다.

### `Pod`와 `Service` 사이의 통신

- 복수의 `Pods`에 대한 트래픽을 관리하기 위해, `Service`라는 추상화를 사용합니다. `Service`는 가상 IP를 통해
  접근되며, 클러스터 내의 `kube-proxy`가 실제 `Pods`로 트래픽을 적절히 전달합니다.

### `DNS`의 작동원리

- 클러스터 내 `Service`나 `Pods`에 대한 도메인 이름 요청은 `CoreDNS`를 통해 해석됩니다. `CoreDNS`는 클러스터
내부의 `Service`나 `Pods`에 대한 도메인 이름을 IP 주소로 매핑하여, 트래픽이 적절한 목적지로 라우팅될 수 있게
합니다.
</details>

## 자세한 설명

<details>
<summary>펼치기</summary>

### 동일한 `Pod`에 속한 `Containers` 간의 통신

#### via

- `localhost`
  - *어떤 `Pod`에 속한 컨테이너들은 `network namespace`*를 공유한다. 모든 Pod가 저마다의 namespace를 갖는
    것.
- `port`

#### `network namespace`란?

## Five essential things

- 동일한 `Pod`에 속한 `Containers` 간의 통신
- 동일한 `node`에 속한 `Pods` 간의 통신
- 서로 다른 `node`에 속한 `Pods` 간의 통신
- `Pod`와 `Service` 사이의 통신
- `DNS`의 작동원리(새로운 `endpoint`를 어떻게 발견할까?)

### 동일한 `Pod`에 속한 `Containers` 간의 통신

#### via

- `localhost`
  - *어떤 `Pod`에 속한 컨테이너들은 `network namespace`*를 공유한다. 모든 Pod가 저마다의 namespace를 갖는
    것.
- `port`

#### `network namespace`란?

`network interfaces`와 `routing tables`의 모음. `namespace`를 통해 논리적으로 레이어를 구분하여 서로 충돌될
일이 없도록 한다.

참고로, Kubernetes의 모든 `Pod`에는 숨겨진 `container(aka pause container)`가 있다. 혹시나 모든 `Pod`가
죽어버렸을 때를 대비해 `namespace`가 open 상태를 유지하는 역할이다.

### 동일한 `node`에 속한 `Pods` 간의 통신

Kubernetes가 세팅한 환경에서 각 `Pods`는 고유한 `network namespace`와 `IP`를 소유하며,
`ethernet device(aka eth0)`를 갖고 있다고 스스로 인식한다.

각 `Pod`는 `eth0`를 통해 외부와 연결되어있는줄 알지만, **사실은 자신이 속한 `(worker)node`의
`virtual ethernet device`로 연결된다.** 즉, 이것(`virtual ethernet device`)은 `node`와 그에 속한 `Pods`간의
통신을 담당한다. 그리고 통신 방향에 따라 각각 `eth0`, `vethX`라 칭한다. (`vethX`는
`virtual ethernet 0, 1, 2` 처럼 `Pods` 마다 통신이 존재하기 때문에 붙여진 이름이다.)

이때 `node`는 `Pods` 간에 통신을 중계하기 위해 `network bridge`를 사용한다. 쿠버네티스에서 이것은 `cbr0`라는
이름으로 생성된다. 모든 `Pods` 및 `node` 조차 이 브릿지에 등록된다.

> `network bridge`는 이름처럼 두 네트워크를 연결한다.

#### service.clusterIP

이 `service`는 `Pod`의 IP를 추상화한 가상 IP를 갖는다. kube-proxy가 이 가상 IP에 매핑된 적절한 `Pod`를 찾아
연결해준다.

왜 추상화가 필요할까? `Pods`는 생성되고 삭제 될 때마다 IP가 바뀌는데, 이를 해결하기 위해 `service`를
생성한다. 예를들어 버전이 업데이트 되거나 망가진 `Pod`를 대체하기 위해 복제본을 재생성하는 경우에 말이다.

### 서로 다른 `node`에 속한 `Pods` 간의 통신

어떤 `Pod`가 다른 node에 있는 `Pod`에 요청을 보내면, 일단은 자기가 속한 node의 bridge로 간다. 물론 그
bridge에는 요청한 IP가 없을 것이다.

그러면 `node`는 이제 default gateway로 'falls back'(_이 의미가 무엇일까?_) 된다. 그리고는 클러스터 레벨까지
올라가서 **router table**에서 IP를 찾는다.

### `Pod`와 `Service` 사이의 통신

고가용성 등을 위하여 우리는 `Pods`를 복제한다. 그런데 이 모든 `Pods`를 위하여 `endpoint`를 각각
관리해야한다면 너무 일이 많다. 그래서 어떤 `service`를 생성하고, 이놈을 개별 endpoint들에 닿기 위한 가상
IP(1차 목적지)로 삼는다.

node마다 존재하는 kube-proxy가 이 가상 IP에 매핑된 적합한 `Pod`를 찾아 연결해준다.

### `DNS`의 작동원리(새로운 `endpoint`를 어떻게 발견할까?)

클러스터에는 DNS를 위한 `service`가 존재한다. 정식 명칭은 `CoreDNS`이지만, 호환성을 위해 `kube-dns`라고
생성된다.

`Pods`는 생성 시 자동으로 클러스터 내부용 domain이 생성되는데, 물론 YAML config에서 임의 설정도 가능하다.

따라서 어떤 `service`에 domain name으로 요청이 들어가면, DNS에서 해당 `service`의 IP를 찾아준다. 이를
전달받은 `kube-proxy`는 이 `service`의 IP가 어느 `Pod`에 연결되는지 알아내어 해당 `Pod`로 요청을 전달한다.

## reference

[Kubernetes Networking Guide for Beginners](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-networking-guide-beginners.html)

</details>
