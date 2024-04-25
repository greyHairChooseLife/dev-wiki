---
title: '일반 사용자로 docker 사용하기 "bye bye sudo"'
birth: '2023-06-24'
modified:
slug: '2023/06/playing-with-docker-without-sudo'
---

도커 쓰는 내내 관리자로 작업하거나, 또는 sudo를 갖다 붙이는 것은 어렵다. shell script를 짜기도 어렵고.

이럴 때 필요한 것이 'docker group'이다. group에 속한 (system)user는 sudo docker를 사용 할 수 있다.

1. `whoami`: username 확인
2. `sudo usermod -aG docker $USER`: 'docker group'에 추가
3. user 로그아웃했다 오거나, 껏다 켠다.

끝!
