# terraform




> [!NOTE] why terraform? - 수동관리의 문제점
>
> - 인프라 재현이 어려움
> - 단순하고 반복적인 작업인데 시간도 오래걸림
> - 휴먼 에러가 발생할 가능성이 높음



> [!rf]
> 
> https://www.youtube.com/watch?v=57FJoXsbf2s



---


- resource == hardware + software + network

    - hardware: 서버, 네트워크 장비(스위치, 라우터), 스토리지(ssd, hard-disk)
    - software: OS, middleware, application
    - network: 인터넷 연결, 서브넷, 보안 정책(inbound/outbound)



    ---
    

- keywords

    - provider
        - e.g. aws, cloudflare, ...
    - module: 공통된 코드를 모듈로 만들어 재사용
    - variable: 같은 모듈 내의 다른 terraform 파일에서 사용할 수 있는 변수
    - backend & state:
        - `terraform apply`를 실행 시 실제 인프라 구성이 어떻게 되었는지 나타내는 명세서
        - 새로운 terraform 파일과 기존 **상태파일을 비교하여 리소스의 변경사항을 추적함**
        - 협업을 위해서는 보통 s3 또는 terraform cloud를 사용함
    - output:
        - provisioning 된 리소스 정보를 외부로 전달
        - 다른 모듈에서 참조 가능
     

---

- workflow

    1. write code
    2. plan    (by running terraform plan)
    3. apply   (by running terraform apply)
    4. destroy (by running terraform destroy)
