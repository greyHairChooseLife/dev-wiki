# 이 문서는 ...

    _chatGPT 질의 응답을 정리함_

## step by step

    1. 대상이 되는 장치(usb drive)의 이름 확인

        `[@bash]$ lsblk`

        - 장치 끼웠을때, 뺐을때 비교하면 딱 나옴

    2. usb drive를 unmount

        `[@bash]$ sudo umount /dev/<my-disk-partition>`

        - 이때 드라이브의 모든 파티션을 unmount 해야 한다.
        - 예시)

            ```bash
            [sy@sy-230620 ~]$ lsblk
            NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
            sda           8:0    0   1.8T  0 disk
            ├─sda1        8:1    0    16M  0 part
            └─sda2        8:2    0   1.8T  0 part
            sdb           8:16   1     0B  0 disk
            sdd           8:48   1 114.6G  0 disk
            ├─sdd1        8:49   1   4.7G  0 part
            ├─sdd2        8:50   1   4.9M  0 part
            ├─sdd3        8:51   1   300K  0 part
            └─sdd4        8:52   1 109.9G  0 part
            sr0          11:0    1  1024M  0 rom
            nvme0n1     259:0    0 476.9G  0 disk
            ├─nvme0n1p1 259:1    0   300M  0 part /boot/efi
            └─nvme0n1p2 259:2    0 476.6G  0 part /

            # 이때 아래와 같이 실행한다.
            sudo umount /dev/sdd1
            sudo umount /dev/sdd2
            sudo umount /dev/sdd3
            sudo umount /dev/sdd4
            ```

        - 앞에 `/dev`가 붙는 이유는 모든 장치는 기본적으로 `/dev`에
          mount 되기 때문이다. device의 dev다.

    3. 파티션 삭제

        `[@bash]$ sudo fdisk /dev/<my-disk-name>`

        - 위 예시에 이어서 아래와 같이 실행한다.

            ```bash
            sudo fdisk /dev/sdd

            # 이후,
            #
            # 1. d를 입력하고 Enter 키를 눌러 파티션 삭제 모드로 들어갑니다.
            # 2. 각 파티션 번호를 입력하여 모든 파티션을 삭제합니다. (1, 2, 3, 4 등)
            # 3. 모든 파티션을 삭제한 후, w를 입력하여 변경 사항을 저장하고 종료합니다.
            ```

    4. 파티션 생성

        `[@bash]$ sudo fdisk /dev/<my-disk-name>`

        - 위 예시에 이어서 아래와 같이 실행한다.

            ```bash
            sudo fdisk /dev/sdd

            # 이후,
            #
            # 1. n를 입력하고 Enter 키를 눌러 새 파티션을 만듭니다.
            # 2. 기본(primary) 파티션을 선택합니다.
            # 3. 파티션 번호를 선택합니다. (일반적으로 1)
            # 4. 기본 시작 섹터를 그대로 사용하고, 마지막 섹터도 기본값을 그대로 사용하여 전체 디스크를 단일 파티션으로 만듭니다.
            # 5. w를 입력하여 변경 사항을 저장하고 종료합니다.
            ```

    5. 파일 시스템 만들기

        `[@bash]$ sudo mkfs.vfat /dev/<my-disk-partition>`

        - mkfs.vfat외에도 다양한 파일 시스템을 활용할 수 있다.
        - ex)
             | 종류    | 커맨드      | 특성                                       |
             | ------- | ----------- | ------------------------------------------ |
             | FAT32   | mkfs.vfat   | 운영체제 호환성 좋지만 파일 최대크기 4GB   |
             | exFAT   | mkfs.exfat  | 대용량 가능                                |
             | NTFS    | mkfs.ntfs   | 대용량 가능                                |

        * 현재 주된 시스템인 manjaro linux에서는 exFAT을 사용할 수 없다.
        * ntfs의 경우 `pacman -Ss ntfs-3g`로 설치해야 했다.
        * `sudo mkfs.ntfs -Q /dev/<my-disk-partition>`으로 실행하니,
          _Cluster size has been automatically set to 4096 bytes._ 라고 메시지가 나온다. 이때 클러스터는 디스크에
          파일이 저장 될 때 최소단위를 말한다. 너무 작다면 큰 파일에 대하여 클러스터링이 너무 많이 일어나서
          성능이 떨어지고, 너무 크다면 아주 작은 파일도 여기서 정하는 최소단위만큼씩 용량을 차지해버린다.
          default인 4KB는 일반적으로 적절하며, 필요에 따라 `-c` 옵션으로 조절할 수 있다.
        * `-Q` 옵션은 빠른 포맷
