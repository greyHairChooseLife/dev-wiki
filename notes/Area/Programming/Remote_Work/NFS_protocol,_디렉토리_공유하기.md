# 󰏢 NFS protocol, 디렉토리 공유하기



network file system의 약자다. 이 역시 server-client 방식이다.

## 서버 설정

1. 필요한 패키지 설치

   `pacman -S nfs-utils`

2. 공유할 디렉토리 설정

   ```bash
   cat >> /etc/exports
   /what/path/to/share  <host>(rw,sync,no_subtree_check)

   # rw: 읽기 및 쓰기 권한
   # sync: 데이터가 디스크에 동기화된 후 클라이언트에 응답
   # no_subtree_check: 하위 디렉토리의 권한을 체크하지 않음으로써 성능을 향상시킴
   ```

   여기서 호스트란 접속할 클라이언트의 IP를 말한다.

3. 설정 적용

   `exportfs -a`

4. (필요시)방화벽에 관련 설정


### 문제 해결

> [!lg] Log 2024-08-30
> - systemctl로 nfs-server를 시작하려하면 dependency가 없다고 에러가 난다. rpcbind이 필수이긴 한데, 이 서비스가 잘 돌아가고 있어도 문제가 발생했다.
>   아치위키에서 말하듯 그냥 재시작 하니까 됐다...


## 클라이언트 설정


1. 클라이언트 패키지를 설치하고 서비스를 시작한다.

2. 마운트한다.

   - 임시로 마운트하기

     `mount <server>:/what/path/to/share /where/to/mount`

   - 영구적으로(자동) 마운트하기

     ```bash
     cat >> /etc/fstab
     <server>:/what/path/to/share /where/to/mount nfs defaults 0 0
     ```


### 문제 해결

> [!lg] Log 2024-08-30
> [-] (미해결)
> - 시스템 부팅 시 자동으로 마운트 되어야하지만, 뭔가 오류가 있는지 그렇지 못하다. 직접 `mount -a` 해주고 있다.
