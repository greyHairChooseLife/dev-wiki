# refactoring with AI from AI-agent-code



## 기존 코드 학습하기


###  Major


- 사용하는 DB
    - postgres
    - redis




#### Prisma Client

**1. 아래 명령으로 prismaClient를 생성하여 사용하게 된다. 각종 메서드가 알아서(?) 생긴다.**

   - File Path: api/Dockerfile, 6:6
     ```dockerfile
     COPY prisma ./prisma
     RUN npx prisma generate
     ```


**2. prisma generate 명령은 일종의 설계도를 보고 client를 생성해준다.**
   -  설계도의 default 경로: `<명령을 실행하는 경로>/prisma/schema.prisma`
   - 아래 **3.의 코드**의 경로에 해당한다.


**3. 설계도에는 결과물이 어디에 생성될지 지정한다.**

   - File Path: api/prisma/schema.prisma, 3:24
     ```prisma
     generator client {
       provider = "prisma-client-js"
       output   = "../src/generated/prisma"
     }
     
     datasource db {
       provider = "postgresql"
       url      = env("DATABASE_URL")
     }
     
     // =============================================================================
     // 1. USERS
     // =============================================================================
     model User {
       id              String    @id @default(dbgenerated("gen_random_uuid()")) @db.Uuid
       identity        String    @unique
       displayName     String?   @map("display_name")
       userType        String?   @map("user_type")  // 'guardian' | 'ward' | null
       email           String?   @unique
       nickname        String?
       profileImageUrl String?   @map("profile_image_url")
       kakaoId         String?   @unique @map("kakao_id")
     ```


**4. PrismaClient를 생성 완료된 경로에서 import 하는 모습을 볼 수 있다.**

   - File Path: api/src/prisma/prisma.service.ts, 1:7
     ```typescript
     import { Injectable, OnModuleInit, OnModuleDestroy, Logger } from '@nestjs/common';
     import { PrismaClient } from '../generated/prisma';
     
     @Injectable()
     export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
       private readonly logger = new Logger(PrismaService.name);
     
     ```



### Minor


#### Facade Pattern


- Facade Pattern은 소프트웨어 디자인 패턴 중 하나
- **복잡한 서브시스템(여러 클래스나 모듈)을 감싸서 간단하고 통일된 인터페이스를 제공**
- 이 패턴의 핵심은 클라이언트 코드가 서브시스템의 세부 사항을 몰라도 쉽게 사용할 수 있도록 하는것
    - 마치 건물의 "퍼사드(외관)"처럼, 내부 복잡성을 숨기고 외부에 깔끔한 인터페이스를 노출

- 주요 목적과 이점
    - **복잡성 감소**: (서브시스템의 여러 컴포넌트를 직접 다루지 않고,) 하나의 Facade 클래스를 통해서만 접근
    - **결합도 낮춤**: 클라이언트가 서브시스템의 변경에 덜 영향을 받음
    - **유지보수 용이**: 서브시스템 내부를 수정해도 Facade 인터페이스가 유지되면 클라이언트 코드를 바꾸지 않아도 됨

##### e.g.

- **데이터베이스 작업에서 여러 `repositories/` 클래스를 직접 호출하는 대신, 하나의 `db.service.ts`가 이를 대신 처리.**

    ```sh
    tree ops_backend/api/src/database/
    │
    ├── repositories
    │   ├── admin.repository.ts
    │   ├── call.repository.ts
    │   ├── dashboard.repository.ts
    │   ├── device.repository.ts
    │   ├── emergency.repository.ts
    │   ├── guardian.repository.ts
    │   ├── index.ts
    │   ├── location.repository.ts
    │   ├── room.repository.ts
    │   ├── user.repository.ts
    │   └── ward.repository.ts
    │
    ├── db.service.ts
    └── // 생략 ...
    ```


- **DB에 접근해야하는 입장에선 이놈만 만나면 그만이다. 일종의 추상화**

    - File Path: api/src/database/`db.service.ts`, 26:54

      ```typescript
      export class DbService implements OnModuleDestroy {
        constructor(
          @Inject('DATABASE_POOL') private readonly pool: Pool,
          private readonly users: UserRepository,
          private readonly devices: DeviceRepository,
          private readonly rooms: RoomRepository,
          private readonly calls: CallRepository,
          private readonly guardians: GuardianRepository,
          private readonly wards: WardRepository,
          private readonly admins: AdminRepository,
          private readonly emergencies: EmergencyRepository,
          private readonly locations: LocationRepository,
          private readonly dashboard: DashboardRepository,
        ) {}
      
        async onModuleDestroy() {
          await this.pool.end();
        }
      
        // ============================================================
        // User methods
        // ============================================================
        async upsertUser(identity: string, displayName?: string) {
          return this.users.upsert(identity, displayName);
        }
      
        async findUserById(userId: string) {
          return this.users.findById(userId);
        }
      ```



#### barrel Export Pattern (aka. index export Pattern)



**Q. 아래와같은 상황에서 왜 export를 두번 하는가?**


  > - **편리한 import 경로**: 외부에서 이 폴더의 모듈을 사용할 때, 파일명을 일일이 지정하지 않아도 돼요.
  >     - 예: `import { WardsController, WardsService } from './wards';` (index.ts를 통해 가져옴)
  >     - 만약 index.ts가 없으면 아래처럼 해야 해요. 폴더에 파일이 많아지면 번거로워집니다.
  >         - `import { WardsController } from './wards/wards.controller';`
  >         - `import { WardsService } from './wards/wards.service';` 
  > 
  > - **공개 API 정의**: 폴더 내부에서 필요한 것만 선택적으로 export할 수 있어요. 이는 **캡슐화(encapsulation)**를 돕습니다.
  >     - 예를 들어, wards 폴더에 내부적으로만 사용하는 헬퍼 파일이 있다고 가정하면, index.ts에서 그걸 export하지 않음으로써 외부에 숨길 수 있어요.
  >     - NestJS 같은 프레임워크에서는 모듈을 import할 때 주로 `import { WardsModule } from './wards';`처럼 index.ts를 활용합니다.
  > 
  > - **유지보수성 향상**: 나중에 파일 구조가 바뀌어도, index.ts만 업데이트하면 외부 import 코드를 바꾸지 않아도 돼요.
  >     - e.g. 파일명을 바꾸거나 폴더를 재구성해도
  > 
  > - **트리 셰이킹(tree shaking)** 지원: 번들러(예: Webpack)가 불필요한 코드를 제거할 때, index.ts를 통해 더 효율적으로 최적화할 수 있어요.


##### e.g.

```sh
tree ops_backend/api/src/wards
.
├── index.ts
├── wards.controller.ts
├── wards.module.ts
└── wards.service.ts

1 directory, 4 files
```




- File Path: api/src/wards/index.ts, 2:3
  ```typescript
  export { WardsController } from './wards.controller';
  export { WardsService } from './wards.service';
  ```


- File Path: api/src/wards/wards.controller.ts, 16:22
  ```typescript
  export class WardsController {
    private readonly logger = new Logger(WardsController.name);
  
    constructor(
      private readonly wardsService: WardsService,
      private readonly authService: AuthService,
    ) {}

    // and so on ...
  ```


- File Path: api/src/wards/wards.service.ts, 5:11
  ```typescript
  export class WardsService {
    private readonly logger = new Logger(WardsService.name);
  
    constructor(private readonly dbService: DbService) {}
  
    async verifyWardAccess(userId: string) {
      const user = await this.dbService.findUserById(userId);

    // and so on ...
  ```




## 어떻게 리팩토링 되는가?

> [!rf]
> 
> REFACTORING_PLAN.md


- api server 위주

- 기존: NestJS의 모듈형 구조를 잘 따르고 있고, DB의 경우 Facade 패턴을 사용
- 변경:

    - **"자립형 모듈"**로써, DB관련 **Facade 패턴을 버리고** 각 모듈이 `dto` 및 `repository`를 소유한다.
    - `common/` 디렉토리에 공통 모듈을 모아둔다.
    - `prisma`, `push`(notification), `ai` 모듈들은 global module로 유지한다.
    - 왜 이렇게 변경할까?
        - 향후 이 기능들이 multi-repo로 찢어지기 좋게 하겠다는 의도
        - 그렇게까지 안하더라도, 우리에겐 **여러 팀원이 공동으로 작업할 때** 가급적 **상호 배타적인 영역만 수정하여 통합 과정을 수월케 하기 위함**

    - ```sh
      # BEFORE
      Controller → [ AppService → DbService ]               → Repository → Prisma

      # AFTER
      Controller → [ Service "(필요시 다른 Service 주입)" ] → Repository → Prisma
      ```

        - 기존: 모든 서비스가 AppService(**God Object**)에 들어있어서 그냥 자기 메서드 여러개 호출하면 됐음
        - 변경:
            - _"필요시 다른 Service 주입"_, 이게 무슨 말이냐? **󰜴 의존성 주입**
                - 각 모듈이 스스로를 노출(Export)
                - 각 모듈들은 필요한 다른 모듈이 있으면 그것을 Import하여(주입 받아) 사용
                - 각 모듈의 스펙(매개변수/반환 or 요청/응답)만 잘 공유하면 됨
            - 왜 이렇게 하냐? **󰜴 각 모듈이 "스펙"에 맞춰 자기 할일만 잘하면 됨"**

            > [!NOTE]
            >
            > - 스펙의 정식 명칭:
            >   - 코드 레벨: **인터페이스(Interface)**, **시그니처(Signature)**
            >   - 데이터 레벨: **DTO**
            >   - 설계 레벨: **계약(Contract)**
            >
            > - 실체
            >   - 지금은 `public` 메서드와 `DTO` 클래스가 그 역할을 하고 있으며
            >   - 나중에는 `Swagger`나 `.proto` 파일이 그 역할을 하게 됩니다.


### 디렉토리 구조 변경

#### AS IS

```sh
api/src/
├── app.service.ts          # GOD OBJECT
├── database/
│   ├── db.service.ts       # GOD OBJECT
│   └── repositories/
│       ├── user.repository.ts
│       ├── device.repository.ts
│       ├── room.repository.ts
│       ├── call.repository.ts
│       ├── ward.repository.ts
│       ├── guardian.repository.ts
│       ├── admin.repository.ts
│       ├── dashboard.repository.ts
│       ├── emergency.repository.ts
│       ├── location.repository.ts
│       └── index.ts
│
│   # services ... call each other when it's needed
├── auth/
├── users/
├── guardians/
├── wards/
├── calls/
├── rtc/
├── devices/
├── push/       # @Global
├── ai/         # @Global
├── events/     # @Global
├── scheduler/
└── admin/
```


#### TO BE

```sh
api/src/
├── common/                 # 공용 (유지)
│   ├── guards/
│   ├── filters/
│   └── decorators/
│
├── prisma/                 # @Global (유지)
│   └── prisma.service.ts
│
├── push/                   # @Global (유지)
│   └── push.service.ts
│
├── ai/                     # @Global (유지)
│   └── ai.service.ts
│
├── calls/                  # 자립형 모듈
│   ├── calls.module.ts
│   ├── calls.controller.ts
│   ├── calls.service.ts      # 신규
│   ├── calls.repository.ts   # database/에서 이동
│   └── dto/
│       ├── invite-call.dto.ts
│       ├── call-response.dto.ts
│       └── index.ts
│
├── users/                  # 자립형 모듈
│   ├── users.module.ts
│   ├── users.controller.ts
│   ├── users.service.ts
│   ├── users.repository.ts   # database/에서 이동
│   └── dto/
│       ├── register-guardian.dto.ts
│       ├── user-response.dto.ts
│       └── index.ts
│
├── ... (다른 모듈들도 동일 구조)
│
├── shared/                 # 신규: 모듈 간 공유
│   ├── types/
│   │   └── database.types.ts  # Row 타입들
│   ├── events/
│   │   ├── call.events.ts
│   │   └── user.events.ts
│   └── constants/
│       └── index.ts
│
└── database/               # 삭제
    ├── db.service.ts       # 삭제
    └── repositories/       # 각 모듈로 이동
```



### 하나의 서비스에서 다른 서비스를 호출해야하는 경우 코드 예시


**단계 1: `UsersModule`에서 Service를 내보내기 (Export)**
다른 모듈에서 `UserService`를 쓸 수 있게 허락해줍니다.

```typescript
// filepath: api/src/users/users.module.ts
import { Module } from '@nestjs/common';
import { UsersService } from './users.service';
import { UsersRepository } from './users.repository';

@Module({
  providers: [UsersService, UsersRepository],
  exports: [UsersService], // 중요: 다른 모듈이 이 서비스를 쓸 수 있게 공개
})
export class UsersModule {}
```

**단계 2: `CallsModule`에서 `UsersModule` 가져오기 (Import)**
`CallsModule`은 `UsersModule`의 기능을 쓰겠다고 선언합니다.

```typescript
// filepath: api/src/calls/calls.module.ts
import { Module } from '@nestjs/common';
import { CallsService } from './calls.service';
import { CallsRepository } from './calls.repository';
import { UsersModule } from '../users/users.module'; // UsersModule import

@Module({
  imports: [UsersModule], // 중요: UsersModule을 가져옴
  providers: [CallsService, CallsRepository],
  controllers: [CallsController],
})
export class CallsModule {}
```

**단계 3: `CallsService`에서 `UserService` 주입받아 사용하기**
이제 생성자(Constructor)를 통해 `UserService`를 주입받아 내 것처럼 씁니다.

```typescript
// filepath: api/src/calls/calls.service.ts
import { Injectable } from '@nestjs/common';
import { CallsRepository } from './calls.repository';
import { UsersService } from '../users/users.service'; // 다른 서비스 import

@Injectable()
export class CallsService {
  constructor(
    private readonly callsRepository: CallsRepository, // 내 레포지토리
    private readonly usersService: UsersService,       // [주입] 다른 서비스
  ) {}

  async startCall(userId: number) {
    // 1. 주입받은 다른 서비스의 메서드 호출
    // (이제 getUserById는 UsersService의 책임입니다)
    const user = await this.usersService.getUserById(userId);

    if (!user.isActive) {
      throw new Error('사용자가 활성 상태가 아닙니다.');
    }

    // 2. 내 레포지토리 사용하여 통화 생성
    return this.callsRepository.createCall(userId);
  }
}
```



### `common/`, `shared/`, `@Global` 모듈은 얼핏 비슷해보이는데 어떻게 구분될까?


- **"역할(Role)"**과 **"의존성 방향(Dependency)"**, 그리고 **"데이터의 성격"**에 따라 구분
- **Vertical Slice Architecture**를 유지하면서, 향후 **멀티 레포(MSA)**로 찢어지기 쉽도록 함

| 구분      | 구분        | 주 내용물                | 성격                   | 로직 유무                |
| :---      | :---        | :---                     | :---                   | :---                     |
| `common/` | 기술 공통   | Guard, Filter, Decorator | NestJS 확장 도구       | ❌ (흐름 제어만)         |
| `shared/` | 데이터 공유 | Types, Events, Constants | 껍데기, 약속, 정의     | ❌ (데이터만)            |
| `@Global` | 기능 공통   | Prisma, Push, AI         | 기능 제공자 (Provider) | ✅ (Business Login 수행) |

#### 차이점 설명

- `common/` (프레임워크 유틸리티)

    > _"NestJS가 요청을 처리하는 방식을 도와주는 도구들"_
    > 
    > - **성격:** **인프라/기술적 관심사 (Technical Concerns)**
    > - **포함 내용:** `Guards` (인증), `Filters` (에러 처리), `Interceptors` (응답 가공), `Decorators` (커스텀 파라미터 등).
    > - **특징:**
    >     - 비즈니스 로직(도메인)을 거의 모릅니다.
    >     - "유저가 로그인했나?", "에러가 나면 어떤 JSON을 줄까?" 같은 **기계적인 처리**를 담당합니다.
    >     - 모든 모듈에서 가져다 쓰지만, 이 폴더 자체가 비즈니스 모듈을 의존하지는 않습니다.
    > - **미래 (멀티 레포 시):** 보통 사내 **NPM 라이브러리**(`@company/nestjs-common`)로 추출되어 여러 마이크로서비스가 공통으로 설치해서 사용하는 형태가 됩니다.
     

- `shared/` (도메인 공유 데이터)
  
    > _"모듈 간에 대화하기 위한 언어(타입, 상수)"_
    > 
    > - **성격:** **수동적인 데이터 정의 (Passive Data)**
    > - **포함 내용:** `Types` (DB Row 타입), `Events` (이벤트 정의 클래스), `Constants` (상수 값).
    > - **특징:**
    >     - **로직(Service)이 없습니다.** 오직 정의(Definition)만 존재합니다.
    >     - **순환 참조 방지용**입니다. 예를 들어 `User` 모듈과 `Call` 모듈이 서로의 타입을 필요로 할 때, 서로를 import 하면 에러가 납니다. 이때 둘 다 `shared`를 바라보게 하여 문제를 해결합니다.
    > - **미래 (멀티 레포 시):**
    >     - `Types`: 각 서버가 공유하는 **DTO 라이브러리**가 되거나, gRPC의 **Proto 파일** 역할을 하게 됩니다.
    >     - `Events`: 메시지 큐(Kafka, RabbitMQ)의 **메시지 스키마**가 됩니다.
 

- `@Global` 모듈 (공통 기능 서비스)
  
    > _"어디서든 불러 쓸 수 있는 능동적인 일꾼들"_
    > 
    > - **성격:** **기능적 서비스 (Functional Services)**
    > - **포함 내용:** `PrismaModule` (DB 연결), `PushModule` (알림 발송), `AiModule` (AI 처리).
    > - **특징:**
    >     - **실제 로직(Service)이 돌아갑니다.**
    >     - NestJS의 `@Global()` 데코레이터를 사용하여, 다른 모듈에서 `imports: [PushModule]`을 매번 쓰지 않아도 자동으로 주입되게 만든 **편의성 모듈**입니다.
    >     - 특정 도메인(User, Call)에 종속되지 않고, "기능" 그 자체를 제공합니다.
    > - **미래 (멀티 레포 시):** 이들은 독립적인 **별도의 마이크로서비스**나 **외부 시스템 연동 어댑터**가 될 가능성이 가장 높습니다. (예: `Push Service` 서버, `AI Service` 서버)
