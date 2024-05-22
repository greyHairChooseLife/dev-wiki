컨테이너를 활용해서 아키텍쳐를 구성한다.

**장점** 

- 확장성
- 이식성
- 개발과 운영의 일관성

## 1. 구성요소

### 1.1. 정적 사이트

   html, css, js파일만 서버에서 제공하면 된다. 즉, 소스코드를 빌드해서 나온 결과물을 nginx같은 가벼운
   웹서버를 통해 제공하면 된다.
   
   ```dockerfile
   FROM nginx:alpine
   COPY ./build /usr/share/nginx/html
   EXPOSE 80
   ```
   
#### 1.1.1. github action을 활용한 빌드, 배포 자동화

- github actions yaml
      
- 무중단 배포  

#### 1.1.2. DB 연동
 
 
### 1.2. 동적 사이트  
 
   1. front-end
   2. back-end
   3. database
