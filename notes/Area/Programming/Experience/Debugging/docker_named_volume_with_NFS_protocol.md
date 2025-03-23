# 󰏢 docker named volume with NFS protocol





## 연결 테스트

_NFS 서버에서 docker.d가 존재하는 클라이언트 서버로의 연결은 이미 확인 한 상태, read-write 권한도 충분_

``` docker-compose.yml
services:
  nfs_test_service:
    image: ubuntu:latest
    container_name: nfs_test_container
    volumes:
      - nfs-volume:/iamvolume
    command: tail -f /dev/null      # 이걸 해야 컨테이너가 종료되지 않음

volumes:
  nfs-volume:
    driver_opts:
      type: "nfs"
      o: "addr=10.71.127.55,rw"
      device: ":/CBPROMAKER4/sy/docker-volumes/patent/v2"
```

