# 공식 추천 설정 for mariadb-connector-nodejs

## 1. timezone

웹서비스 관점에서 front-end, back-end, database 서버들은 각자의 timezone을 가질 수 있다. 일관성을 유지하되
각자의 지리적 위치에 맞는 시간대를 사용하려면 back-end 및 database 서버의 timezone을 UTC로 설정하고,
front-end에서 엔드유저에게 보여줄 때만 현지화해서 표시해주는게 좋다.

### 1.1. back-end server 설정

(내가)주로 사용하는 nodejs 환경을 기준을로 설명한다. timezone을 아래처럼 설정하면 된다. 

```javascript
const mariadb = require('mariadb');

const conn = mariadb.createConnection({
  host: ..., 
  user: ..., 
  password: ...,
  timezone: 'UTC',
  skipSetTimezone: false
});
```

이때 `skipSetTimezone` 옵션은 바로위의 `timezone` 옵션을 skip할지 말지 설정한다. default값이 false이지만
명시적으로 표현했다.

> 그 위에 timezone은 기본값이 `local`이고, 시스템 시간대를 사용한단거다. 이것이 `skipSetTimezone:
> false(default)`와 합쳐진 형태가 가장 간단히 사용하는 형태인 것이다.  
>
> 이 상태로 backend-server(system)의 타임존만 맞추고 사용하는게 보통인듯 하다. `WAS`만 잘 설정하면
> 간결하고 또 충분하겠지만 시스템의 기준시도 통일하면 나쁠게 없다.
> 
> on-premise 환경이라도 container를 사용하는게 일반적이니 컨테이너의 timezone을 설정해주자.
> ```bash
> docker run ... -e TZ=UTC ...
> ```
  

### 1.2. database 설정

init sql에 아래처럼 넣어주자.

> ```sql
> SET time_zone = '+00:00';
> ```

`SELECT @@global.time_zone;`으로 확인해보면 잘 설정되었는지 확인할 수 있다.(기본값은 `SYSTEM`이다.)

`SELECT NOW()`로도 현재시간을 확인해보자.

## TLS (aka ssl)

TLS를 적용해 들어온 요청만 처리할 수 있도록 설정하는 것이다. 때때로 인증서 관리를 못했을 때를 대비하여
중요 정보를 보호할 수 있겠다.

설정이 간단해보이니 필요시 아래 reference를 참고하자.

## \*Reference 

- [mariadb knowledge base document](https://github.com/mariadb-corporation/mariadb-connector-nodejs/blob/master/documentation/callback-api.md) 
