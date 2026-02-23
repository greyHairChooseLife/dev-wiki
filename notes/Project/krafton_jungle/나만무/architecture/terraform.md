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



---


- 동적 실행

    - 방법-A
        - 실행
            ```bash
            # cpu_test 모듈만 실행 (기존 t3 인스턴스는 건드리지 않음)
            terraform apply -target=module.cpu_test
            # 제거
            terraform destroy -target=module.cpu_test
            ```

    - 방법-B
        ```terraform
        variable "enable_cpu_test" {
          description = "C7i 테스트 인스턴스 생성 여부"
          type        = bool
          default     = false
        }

        module "cpu_test" {
          source = "./modules/c7i-test"
          
          # 변수가 true일 때만 모듈 생성 (0이면 생성 안 함)
          count = var.enable_cpu_test ? 1 : 0

          ami_id            = "ami-092fba6d9bb44f9c8" # 아까 확인한 기존 t3의 이미지
          key_name          = "dev-server"
          security_group_id = data.aws_security_group.example.id
          name              = "c7i-xlarge-benchmark"
        }
        ```

        - 실행
            ```bash
            # 테스트 서버 켜기
            terraform apply -var="enable_cpu_test=true"

            # 테스트 서버 끄기 (모듈 내용 삭제)
            terraform apply -var="enable_cpu_test=false"
            ```

