---
hide:
title: '차근차근 설치하기'
date: 2024-04-15
update:
---

## 환경 구성하기

> [k8s 공식 안내](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

1. docker 설치

   > container runtime으로 설치하는것

2. kubernetes 설치

   > maste node, worker node 모두 설치해야한다.

   1. 환경설정

      - swap 사용하지 않기
      - 방화벽 끄기: 방화벽은 보통 훨씬 앞쪽에서 활성화 되어있다.  
        (또는 지정된 포트만 열어줄 수도 있다.)

      <br/>

   2. kubeadm, kubectl, kubelet 설치

      - **kubeadm**: the command to bootstrap the cluster.
      - **kubelet**: the component that runs on all of the machines in your cluster and does things like
        starting pods and containers.
      - **kubectl**: the command line util to talk to your cluster.

      - <details>
           <summary>우분투, 만자로 cli install</summary>

        ```shell
        # ubuntu 22.04.4

        apt-get update
        apt-get install -y apt-transport-https ca-certificates curl gpg

        curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

        # Add the appropriate Kubernetes apt repository. Please note that this repository have packages only for Kubernetes 1.29; for other Kubernetes minor versions, you need to change the Kubernetes minor version in the URL
        echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

        apt-get update
        apt-get install -y kubelet kubeadm kubectl
        apt-mark hold kubelet kubeadm kubectl

        # manjaro

        pacman -S kubectl kubeadm kubelet

        # (공통)Daemon 실행
        systemctl enable kubelet && systemctl start kubelet
        ```

       </details><br>

3. config control plane

   아래 명령어를 통해 `master node`에서 *API 서버, 스케줄러, 컨트롤러 매니저, etcd, CoreDNS*를 실행한다.

   ```shell
   # master node에서만

   kubeadm init
   ```

   실행이 완료되면 아래와 같은 안내 메시지가 나온다. 잘 저장해두고 사용하자.

   1. root 또는 일반 유저가 kubectl을 사용할 수 있도록 권한을 부여하는 명령어
   2. worker node를 추가할 때 필요한 join 명령어

   <br>
   권한 부여 이후 `kubectl get nodes` 명령어를 통해 node가 정상적으로 등록되었는지 확인한다. 혹시 잘 안되면 runtime인 kubelet을 재시작해 주자.
   <br><br>

   <details>
     <summary>펼침과 같이 나온다면 성공</summary>

   ```shell
   $ kubectl get nodes;
   NAME        STATUS     ROLES           AGE   VERSION
   sy-230620   NotReady   control-plane   37m   v1.29.2
   ```

   </details><br>

   그런데 잘 보면 STATUS가 **NotReady**로 나온다. 왜냐면 CNI(Container Network Interface)가 설치되어있지
   않아서 그렇다.

   <details>
     <summary>weave network add-on 설치</summary>

   ```shell
   # weave network를 설치한다. 종류는 엄청 많다.

   $ kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
   ```

   - [installation guide](https://github.com/weaveworks/weave/blob/master/site/kubernetes/kube-addon.md#-installation)

   </details><br>
