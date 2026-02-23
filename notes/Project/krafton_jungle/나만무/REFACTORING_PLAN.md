# OPS Backend 리팩토링 및 멀티 레포 마이그레이션 계획

> Vertical Slice Architecture 기반 리팩토링 후 멀티 레포 분리

**버전**: 2.2.0
**작성일**: 2025-12-31
**상태**: 계획 수립 + 일부 구현 진행

---

## 목차

1. [현재 상태 분석](#1-현재-상태-분석)
2. [목표 아키텍처](#2-목표-아키텍처)
3. [Phase 1: 모듈별 완전 리팩토링](#3-phase-1-모듈별-완전-리팩토링)
4. [Phase 2: 서버 간 통신 준비](#4-phase-2-서버-간-통신-준비)
5. [Phase 2.5: 레포 분리 준비](#5-phase-25-레포-분리-준비)
6. [Phase 3: 멀티 레포 분리](#6-phase-3-멀티-레포-분리)
7. [Phase 4: 인프라 및 배포](#7-phase-4-인프라-및-배포)
8. [실행 계획](#8-실행-계획)

---

## 1. 현재 상태 분석

### 1.1 God Objects (삭제 대상)

| 파일 | 라인 수 | 문제점 |
|------|--------|--------|
| `app.service.ts` | 505 | 모든 비즈니스 로직 집중, 30개 파일에서 의존 |
| `db.service.ts` | 572 | Facade 패턴, 70+ 메서드, 모든 Repository 래핑 |
| **합계** | **1,077** | 분리 시 어디에 넣을지 결정 불가 |

### 1.2 현재 구조

```
api/src/
├── app.service.ts          ← GOD OBJECT (비즈니스 로직)
├── database/
│   ├── db.service.ts       ← GOD OBJECT (Facade) - 삭제 예정
│   └── repositories/       ← 11개 중앙 집중 - 각 모듈로 분산
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
├── auth/           ✅ Service 있음, ❌ Repository 없음
├── users/          ✅ Service 있음, ❌ Repository 없음
├── guardians/      ✅ Service 있음, ❌ Repository 없음
├── wards/          ✅ Service 있음, ❌ Repository 없음
├── calls/          ❌ Service 없음, ❌ Repository 없음
├── rtc/            ❌ Service 없음, ❌ Repository 없음
├── devices/        ❌ Service 없음, ❌ Repository 없음
├── push/           ✅ Service 있음 (@Global)
├── ai/             ✅ Service 있음 (@Global)
├── events/         ✅ Service 있음 (@Global) - SSE 실시간 이벤트
├── scheduler/      ✅ Scheduler 있음
└── admin/
    ├── auth/       ✅ Service 있음
    ├── dashboard/  ❌ Service 없음
    ├── emergencies/❌ Service 없음
    ├── locations/  ❌ Service 없음
    └── wards-management/ ❌ Service 없음
```

### 1.3 DTO 상태

| 모듈 | 현재 상태 |
|------|----------|
| auth/ | ✅ 3개 (kakao, refresh, anonymous) |
| users/ | ✅ 2개 (register, response) |
| calls/ | ❌ 빈 폴더 |
| devices/ | ❌ 빈 폴더 |
| guardians/ | ❌ 빈 폴더 |
| wards/ | ❌ 빈 폴더 |
| rtc/ | ❌ 빈 폴더 |
| push/ | ❌ 빈 폴더 |
| admin/* | ❌ 없음 |

### 1.4 의존성 현황

```
30개 파일이 AppService 또는 DbService에 의존
├── Controllers: 직접 AppService 호출
├── Services: DbService 통해 Repository 접근
└── Schedulers: 양쪽 모두 사용
```

---

## 2. 목표 아키텍처

### 2.1 Vertical Slice Architecture

각 모듈이 자체 Repository, Service, DTO를 포함하는 자립형 구조:

```
api/src/
├── common/                 ← 공용 (유지)
│   ├── guards/
│   ├── filters/
│   └── decorators/
│
├── prisma/                 ← @Global (유지)
│   └── prisma.service.ts
│
├── push/                   ← @Global (유지)
│   └── push.service.ts
│
├── ai/                     ← @Global (유지)
│   └── ai.service.ts
│
├── calls/                  ← 자립형 모듈
│   ├── calls.module.ts
│   ├── calls.controller.ts
│   ├── calls.service.ts      ← 신규
│   ├── calls.repository.ts   ← database/에서 이동
│   └── dto/
│       ├── invite-call.dto.ts
│       ├── call-response.dto.ts
│       └── index.ts
│
├── users/                  ← 자립형 모듈
│   ├── users.module.ts
│   ├── users.controller.ts
│   ├── users.service.ts
│   ├── users.repository.ts   ← database/에서 이동
│   └── dto/
│       ├── register-guardian.dto.ts
│       ├── user-response.dto.ts
│       └── index.ts
│
├── ... (다른 모듈들도 동일 구조)
│
├── shared/                 ← 신규: 모듈 간 공유
│   ├── types/
│   │   └── database.types.ts  ← Row 타입들
│   ├── events/
│   │   ├── call.events.ts
│   │   └── user.events.ts
│   └── constants/
│       └── index.ts
│
└── database/               ← 삭제
    ├── db.service.ts       ❌ 삭제
    └── repositories/       ❌ 각 모듈로 이동
```

### 2.2 변경 전후 비교

```
[BEFORE]
Controller → AppService → DbService → Repository → Prisma

[AFTER]
Controller → Service → Repository → Prisma
              ↓
        (필요시 다른 Service 주입)
```

### 2.3 @Global 모듈 (공유)

| 모듈 | 역할 | 유지 이유 |
|------|------|----------|
| PrismaModule | DB 연결 | 모든 Repository가 사용 |
| PushModule | APNs 발송 | 여러 모듈에서 사용 |
| AiModule | AI 분석 | 여러 모듈에서 사용 |
| EventsModule | SSE 실시간 이벤트 | 로그아웃/탈퇴 등 상태 변경 알림 (✅ 구현 완료) |

---

## 3. Phase 1: 모듈별 완전 리팩토링

### 3.1 리팩토링 순서

핵심 도메인부터 순차적으로 진행:

```
1. calls/      ← 가장 핵심, 의존성 적음
2. rooms/      ← calls와 밀접 (신규 모듈)
3. devices/    ← 독립적
4. users/      ← 기반 엔티티
5. wards/      ← users 의존
6. guardians/  ← users, wards 의존
7. auth/       ← users 의존
8. rtc/        ← rooms 의존
9. admin/*     ← 마지막 (가장 복잡)
10. 정리       ← AppService, DbService 삭제
```

---

### 3.2 모듈별 상세 계획

#### 3.2.1 calls/ 모듈

**현재 상태:**
```typescript
// calls.controller.ts
constructor(
  private readonly appService: AppService,  // ❌ God Object
  private readonly aiService: AiService,
  private readonly notificationScheduler: NotificationScheduler,
) {}
```

**목표 상태:**
```typescript
// calls.controller.ts
constructor(
  private readonly callsService: CallsService,  // ✅ 전용 Service
) {}
```

**작업 내용:**

| 파일 | 작업 |
|------|------|
| `calls.repository.ts` | `database/repositories/call.repository.ts` 이동 |
| `calls.service.ts` | 신규 생성, AppService에서 로직 이동 |
| `dto/invite-call.dto.ts` | 신규 생성 |
| `dto/answer-call.dto.ts` | 신규 생성 |
| `dto/call-response.dto.ts` | 신규 생성 |
| `calls.controller.ts` | AppService → CallsService로 변경 |
| `calls.module.ts` | Repository, Service 등록 |

**DTO 정의:**

```typescript
// dto/invite-call.dto.ts
export class InviteCallDto {
  callerIdentity: string;
  callerName?: string;
  calleeIdentity: string;
  roomName?: string;
}

// dto/call-response.dto.ts
export class CallResponseDto {
  callId: string;
  roomName: string;
  state: 'invited' | 'answered' | 'ended';
  callerIdentity: string;
  calleeIdentity: string;
  createdAt: Date;
  answeredAt?: Date;
  endedAt?: Date;
}

// dto/call-summary.dto.ts
export class CallSummaryDto {
  id: string;
  callId: string;
  wardId: string;
  summary: string | null;
  mood: string | null;
  healthKeywords: string[];
  topics: string[];
  painMentions: string[];
  analyzedAt: Date;
}
```

**Service 구조:**

```typescript
// calls.service.ts
@Injectable()
export class CallsService {
  constructor(
    private readonly callsRepository: CallsRepository,
    private readonly roomsService: RoomsService,
    private readonly pushService: PushService,
    private readonly devicesRepository: DevicesRepository,
  ) {}

  async invite(dto: InviteCallDto): Promise<CallResponseDto> {
    // 1. Room 생성/조회
    // 2. Call 레코드 생성
    // 3. 푸시 발송
    // 4. DTO 변환 후 반환
  }

  async answer(callId: string): Promise<CallResponseDto> { ... }
  async end(callId: string): Promise<CallResponseDto> { ... }

  private toResponseDto(row: CallRow): CallResponseDto {
    return {
      callId: row.id,
      roomName: row.room_name,
      state: row.state as CallResponseDto['state'],
      callerIdentity: row.caller_identity,
      calleeIdentity: row.callee_identity,
      createdAt: new Date(row.created_at),
      answeredAt: row.answered_at ? new Date(row.answered_at) : undefined,
      endedAt: row.ended_at ? new Date(row.ended_at) : undefined,
    };
  }
}
```

---

#### 3.2.2 rooms/ 모듈 (신규)

현재 room 관련 로직이 흩어져 있으므로 별도 모듈로 분리:

```
rooms/
├── rooms.module.ts
├── rooms.controller.ts      ← rtc.controller.ts에서 분리 (GET /rooms/:name/members)
├── rooms.service.ts         ← 신규
├── rooms.repository.ts      ← database/repositories/room.repository.ts 이동
└── dto/
    ├── room-response.dto.ts
    ├── room-member.dto.ts
    └── index.ts
```

**DTO 정의:**

```typescript
// dto/room-response.dto.ts
export class RoomResponseDto {
  id: string;
  roomName: string;
  createdAt: Date;
}

// dto/room-member.dto.ts
export class RoomMemberDto {
  identity: string;
  displayName: string | null;
  joinedAt: Date;
}
```

---

#### 3.2.3 devices/ 모듈

**작업 내용:**

```
devices/
├── devices.module.ts
├── devices.controller.ts    ← 기존 유지
├── devices.service.ts       ← 신규
├── devices.repository.ts    ← database/에서 이동
└── dto/
    ├── register-device.dto.ts
    ├── device-response.dto.ts
    └── index.ts
```

**DTO 정의:**

```typescript
// dto/register-device.dto.ts
export class RegisterDeviceDto {
  identity: string;
  platform: 'ios' | 'android' | 'web';
  apnsToken?: string;
  voipToken?: string;
  supportsCallKit?: boolean;
  env?: 'sandbox' | 'production';
}

// dto/device-response.dto.ts
export class DeviceResponseDto {
  id: string;
  platform: string;
  hasApnsToken: boolean;
  hasVoipToken: boolean;
  supportsCallKit: boolean;
  env: string;
  lastSeen: Date;
}
```

---

#### 3.2.4 users/ 모듈

**현재 상태:** Service 있음, Repository 없음

**작업 내용:**

| 파일 | 작업 |
|------|------|
| `users.repository.ts` | `database/repositories/user.repository.ts` 이동 |
| `users.service.ts` | Repository 직접 사용하도록 수정 |
| `dto/` | 기존 유지 + 추가 |

**추가 DTO:**

```typescript
// dto/user-profile.dto.ts
export class UserProfileDto {
  id: string;
  identity: string;
  displayName: string | null;
  userType: 'guardian' | 'ward' | null;
  email: string | null;
  nickname: string | null;
  profileImageUrl: string | null;
  createdAt: Date;
}
```

---

#### 3.2.5 wards/ 모듈

**작업 내용:**

```
wards/
├── wards.module.ts
├── wards.controller.ts
├── wards.service.ts         ← Repository 직접 사용
├── wards.repository.ts      ← database/에서 이동
└── dto/
    ├── ward-response.dto.ts
    ├── ward-settings.dto.ts
    ├── ward-location.dto.ts
    └── index.ts
```

**DTO 정의:**

```typescript
// dto/ward-response.dto.ts
export class WardResponseDto {
  id: string;
  userId: string;
  phoneNumber: string;
  guardianId: string | null;
  organizationId: string | null;
  aiPersona: string;
  weeklyCallCount: number;
  callDurationMinutes: number;
  createdAt: Date;
}

// dto/ward-settings.dto.ts
export class UpdateWardSettingsDto {
  aiPersona?: string;
  weeklyCallCount?: number;
  callDurationMinutes?: number;
}

// dto/ward-location.dto.ts
export class WardLocationDto {
  id: string;
  wardId: string;
  latitude: number;
  longitude: number;
  accuracy: number | null;
  address: string | null;
  recordedAt: Date;
  batteryLevel: number | null;
}
```

---

#### 3.2.6 guardians/ 모듈

**작업 내용:**

```
guardians/
├── guardians.module.ts
├── guardians.controller.ts
├── guardians.service.ts     ← Repository 직접 사용
├── guardians.repository.ts  ← database/에서 이동
└── dto/
    ├── guardian-response.dto.ts
    ├── guardian-dashboard.dto.ts
    ├── guardian-report.dto.ts
    └── index.ts
```

**DTO 정의:**

```typescript
// dto/guardian-dashboard.dto.ts
export class GuardianDashboardDto {
  wardCount: number;
  totalCalls: number;
  averageMood: string;
  recentAlerts: HealthAlertDto[];
  upcomingCalls: ScheduledCallDto[];
}

// dto/guardian-report.dto.ts
export class GuardianReportDto {
  wardId: string;
  wardName: string;
  period: { start: Date; end: Date };
  callSummaries: CallSummaryDto[];
  moodTrend: MoodTrendDto[];
  healthKeywords: string[];
}
```

---

#### 3.2.7 auth/ 모듈

**현재 상태:** Service 있음, DTO 있음, Repository 없음

**작업 내용:**
- UsersRepository 주입받아 사용 (User 생성은 users 모듈 담당)
- RefreshToken 관련 로직은 UsersRepository에 위임

```typescript
// auth.service.ts 수정
@Injectable()
export class AuthService {
  constructor(
    private readonly usersRepository: UsersRepository,  // 직접 주입
  ) {}
}
```

---

#### 3.2.8 rtc/ 모듈

**현재 상태:** Controller만 있음, Service 없음

**✅ 완료된 작업:**
- Identity 우선순위 변경: `authIdentity ?? body.identity` (인증된 identity 우선)
- iOS 앱에서 보내는 임의 identity 대신 카카오 인증 identity 사용으로 일관성 확보

**작업 내용:**

```
rtc/
├── rtc.module.ts
├── rtc.controller.ts        ← 토큰 발급만 (identity 우선순위 수정됨)
├── rtc.service.ts           ← 신규
└── dto/
    ├── rtc-token-request.dto.ts
    ├── rtc-token-response.dto.ts
    └── index.ts
```

**DTO 정의:**

```typescript
// dto/rtc-token-request.dto.ts
export class RtcTokenRequestDto {
  identity: string;
  name?: string;
  roomName: string;
  role?: 'host' | 'viewer' | 'observer';
}

// dto/rtc-token-response.dto.ts
export class RtcTokenResponseDto {
  token: string;
  identity: string;
  roomName: string;
}
```

---

#### 3.2.9 admin/ 모듈

admin은 서브 모듈들을 포함하는 구조 유지:

```
admin/
├── admin.module.ts
├── auth/
│   ├── admin-auth.controller.ts
│   ├── admin-auth.service.ts
│   ├── admin.repository.ts      ← database/에서 이동
│   └── dto/
│       ├── admin-login.dto.ts
│       └── admin-response.dto.ts
│
├── dashboard/
│   ├── dashboard.controller.ts
│   ├── dashboard.service.ts     ← 신규
│   ├── dashboard.repository.ts  ← database/에서 이동
│   └── dto/
│       ├── dashboard-stats.dto.ts
│       └── realtime-data.dto.ts
│
├── emergencies/
│   ├── emergencies.controller.ts
│   ├── emergencies.service.ts   ← 신규
│   ├── emergencies.repository.ts ← database/에서 이동
│   └── dto/
│       ├── emergency-response.dto.ts
│       └── create-emergency.dto.ts
│
├── locations/
│   ├── locations.controller.ts
│   ├── locations.service.ts     ← 신규
│   ├── locations.repository.ts  ← database/에서 이동
│   └── dto/
│       └── location-response.dto.ts
│
└── wards-management/
    ├── wards-management.controller.ts
    ├── wards-management.service.ts  ← 신규
    └── dto/
        ├── bulk-upload.dto.ts
        └── ward-list.dto.ts
```

---

#### 3.2.10 정리: God Objects 삭제

모든 모듈 리팩토링 완료 후:

| 파일 | 작업 |
|------|------|
| `app.service.ts` | 삭제 (로직 각 모듈로 이동 완료) |
| `database/db.service.ts` | 삭제 |
| `database/repositories/` | 폴더 삭제 (모두 이동 완료) |
| `database/database.module.ts` | 삭제 |

**최종 구조:**

```
api/src/
├── shared/
│   ├── types/
│   │   └── database.types.ts    ← Row 타입들 (types.ts에서 이동)
│   ├── mappers/
│   │   └── prisma-mappers.ts    ← 매퍼 함수들
│   └── constants/
│       └── index.ts
│
├── (database/ 폴더 삭제됨)
│
├── calls/
│   ├── calls.module.ts
│   ├── calls.controller.ts
│   ├── calls.service.ts
│   ├── calls.repository.ts
│   └── dto/
│
├── rooms/
│   └── ...
│
└── ... (각 모듈 자립)
```

---

### 3.3 shared/ 폴더 구조

모듈 간 공유가 필요한 타입들:

```typescript
// shared/types/database.types.ts
export type UserRow = { ... };
export type WardRow = { ... };
export type CallRow = { ... };
// ... 기존 types.ts 내용

// shared/events/call.events.ts
export interface CallStartedEvent {
  type: 'call.started';
  callId: string;
  roomName: string;
  callerIdentity: string;
  calleeIdentity: string;
  timestamp: Date;
}

export interface CallEndedEvent {
  type: 'call.ended';
  callId: string;
  duration: number;
  timestamp: Date;
}

// shared/events/user.events.ts
export interface UserCreatedEvent {
  type: 'user.created';
  userId: string;
  userType: 'guardian' | 'ward' | null;
  timestamp: Date;
}
```

---

## 4. Phase 2: 서버 간 통신 준비

### 4.1 Event Bus (Redis Pub/Sub)

멀티 레포 분리 후 서버 간 비동기 통신:

```typescript
// infrastructure/event-bus/event-bus.interface.ts
export interface EventBus {
  publish<T>(channel: string, event: T): Promise<void>;
  subscribe<T>(channel: string, handler: (event: T) => void): void;
}

// infrastructure/event-bus/redis-event-bus.ts
@Injectable()
export class RedisEventBus implements EventBus {
  constructor(private readonly redis: Redis) {}

  async publish<T>(channel: string, event: T): Promise<void> {
    await this.redis.publish(channel, JSON.stringify(event));
  }

  subscribe<T>(channel: string, handler: (event: T) => void): void {
    const subscriber = this.redis.duplicate();
    subscriber.subscribe(channel);
    subscriber.on('message', (ch, message) => {
      if (ch === channel) {
        handler(JSON.parse(message));
      }
    });
  }
}
```

### 4.2 Internal API Client

서버 간 동기 HTTP 통신:

```typescript
// infrastructure/internal/internal.module.ts
@Module({
  imports: [HttpModule],
  providers: [InternalApiClient],
  exports: [InternalApiClient],
})
export class InternalModule {}

// infrastructure/internal/internal-api.client.ts
@Injectable()
export class InternalApiClient {
  constructor(private readonly http: HttpService) {}

  private getHeaders(): Record<string, string> {
    return {
      'X-Internal-Auth': process.env.INTERNAL_AUTH_SECRET,
      'X-Service-Name': process.env.SERVICE_NAME,
    };
  }

  async getUser(userId: string): Promise<UserProfileDto> {
    const url = `${process.env.OPS_API_URL}/internal/users/${userId}`;
    const response = await this.http.get(url, {
      headers: this.getHeaders(),
    }).toPromise();
    return response.data;
  }
}
```

### 4.3 Internal Endpoints

각 서비스에 내부용 엔드포인트 추가:

```typescript
// internal/internal.controller.ts
@Controller('internal')
@UseGuards(InternalAuthGuard)
export class InternalController {
  constructor(private readonly usersService: UsersService) {}

  @Get('users/:id')
  async getUser(@Param('id') id: string) {
    return this.usersService.findById(id);
  }
}

// internal/internal-auth.guard.ts
@Injectable()
export class InternalAuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    const token = request.headers['x-internal-auth'];
    return token === process.env.INTERNAL_AUTH_SECRET;
  }
}
```

---

## 5. Phase 2.5: 레포 분리 준비

> Phase 2까지 완료 후, 폴더 복사만으로 레포 분리가 가능하도록 준비

### 5.1 왜 필요한가?

Phase 2 완료 후에도 폴더 복사만으로는 레포가 되지 않습니다:

| 항목 | 문제점 | 해결 |
|------|--------|------|
| `shared/` | 로컬 경로 import | npm 패키지로 분리 |
| `app.module.ts` | 모든 모듈 import | 레포별 별도 작성 |
| `main.ts` | 단일 포트 | 레포별 포트 설정 |
| `admin/*` | 중첩 구조 | 루트 레벨로 평탄화 |

### 5.2 ops-shared npm 패키지 추출

**Step 1: 패키지 구조 생성**

```
packages/
└── ops-shared/
    ├── src/
    │   ├── types/
    │   │   ├── user.types.ts
    │   │   ├── ward.types.ts
    │   │   ├── call.types.ts
    │   │   ├── device.types.ts
    │   │   └── index.ts
    │   ├── events/
    │   │   ├── call.events.ts
    │   │   ├── user.events.ts
    │   │   └── index.ts
    │   ├── mappers/
    │   │   └── prisma-mappers.ts
    │   └── index.ts
    ├── package.json
    └── tsconfig.json
```

**Step 2: package.json**

```json
{
  "name": "@ops/shared",
  "version": "1.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "files": ["dist"],
  "scripts": {
    "build": "tsc",
    "prepublishOnly": "npm run build"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  }
}
```

**Step 3: api/에서 import 경로 변경**

```typescript
// Before
import { UserRow, CallRow } from '../shared/types';
import { toUserRow } from '../shared/mappers/prisma-mappers';

// After
import { UserRow, CallRow, toUserRow } from '@ops/shared';
```

### 5.3 레포별 app.module.ts 템플릿

**ops-api용 (api/src/app.module.ops-api.ts)**

```typescript
import { Module } from '@nestjs/common';
import { ScheduleModule } from '@nestjs/schedule';

// 공용 모듈
import { CommonModule } from './common';
import { PrismaModule } from './prisma';
import { PushModule } from './push';
import { AiModule } from './ai';

// 도메인 모듈 (admin 제외)
import { AuthModule } from './auth';
import { UsersModule } from './users';
import { GuardiansModule } from './guardians';
import { WardsModule } from './wards';
import { CallsModule } from './calls';
import { RoomsModule } from './rooms';
import { DevicesModule } from './devices';
import { RtcModule } from './rtc';
import { SchedulerModule } from './scheduler';

// 인프라
import { InternalModule } from './internal';
import { InfrastructureModule } from './infrastructure';

@Module({
  imports: [
    ScheduleModule.forRoot(),
    CommonModule,
    PrismaModule,
    PushModule,
    AiModule,
    AuthModule,
    UsersModule,
    GuardiansModule,
    WardsModule,
    CallsModule,
    RoomsModule,
    DevicesModule,
    RtcModule,
    SchedulerModule,
    InternalModule,
    InfrastructureModule,
  ],
})
export class AppModule {}
```

**ops-admin-api용 (api/src/app.module.ops-admin-api.ts)**

```typescript
import { Module } from '@nestjs/common';

// 공용 모듈
import { CommonModule } from './common';
import { PrismaModule } from './prisma';

// Admin 도메인 모듈 (평탄화됨)
import { AdminAuthModule } from './auth';
import { DashboardModule } from './dashboard';
import { EmergenciesModule } from './emergencies';
import { LocationsModule } from './locations';
import { WardsManagementModule } from './wards-management';

// 인프라
import { InternalModule } from './internal';
import { InfrastructureModule } from './infrastructure';

@Module({
  imports: [
    CommonModule,
    PrismaModule,
    AdminAuthModule,
    DashboardModule,
    EmergenciesModule,
    LocationsModule,
    WardsManagementModule,
    InternalModule,
    InfrastructureModule,
  ],
})
export class AppModule {}
```

### 5.4 레포별 main.ts 템플릿

**ops-api용 (api/src/main.ops-api.ts)**

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors();
  await app.listen(process.env.PORT || 8080);
}
bootstrap();
```

**ops-admin-api용 (api/src/main.ops-admin-api.ts)**

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.enableCors();
  app.setGlobalPrefix('admin');  // /admin prefix
  await app.listen(process.env.PORT || 8081);
}
bootstrap();
```

### 5.5 admin/ 모듈 평탄화

분리 전 admin/* 구조를 루트 레벨로 준비:

```
현재 (api/src/admin/):
├── admin.module.ts
├── auth/
│   ├── admin-auth.controller.ts
│   └── admin-auth.service.ts
├── dashboard/
├── emergencies/
├── locations/
└── wards-management/

분리 후 (ops-admin-api/src/):
├── auth/                    ← admin/auth 이동
│   ├── auth.module.ts       ← admin-auth → auth 리네임
│   ├── auth.controller.ts
│   └── auth.service.ts
├── dashboard/               ← admin/dashboard 이동
├── emergencies/             ← admin/emergencies 이동
├── locations/               ← admin/locations 이동
└── wards-management/        ← admin/wards-management 이동
```

**리네임 스크립트 준비 (scripts/flatten-admin.sh)**

```bash
#!/bin/bash
# ops-admin-api 생성 시 실행

SRC="api/src/admin"
DST="ops-admin-api/src"

# 모듈 복사 및 리네임
for dir in auth dashboard emergencies locations wards-management; do
  cp -r "$SRC/$dir" "$DST/"
done

# admin-auth → auth 리네임
mv "$DST/auth/admin-auth.controller.ts" "$DST/auth/auth.controller.ts"
mv "$DST/auth/admin-auth.service.ts" "$DST/auth/auth.service.ts"
mv "$DST/auth/admin-auth.module.ts" "$DST/auth/auth.module.ts" 2>/dev/null || true

# import 경로 수정 (sed)
find "$DST" -name "*.ts" -exec sed -i 's/admin-auth/auth/g' {} \;
```

### 5.6 Phase 2.5 완료 후 구조

```
api/src/
├── packages/
│   └── ops-shared/          ← npm 패키지 (publish 준비)
│
├── app.module.ts            ← 현재용 (모든 모듈)
├── app.module.ops-api.ts    ← ops-api용 템플릿
├── app.module.ops-admin-api.ts ← ops-admin-api용 템플릿
│
├── main.ts                  ← 현재용
├── main.ops-api.ts          ← ops-api용 템플릿
├── main.ops-admin-api.ts    ← ops-admin-api용 템플릿
│
├── common/
├── prisma/
├── push/
├── ai/
├── infrastructure/
├── internal/
│
├── auth/                    ─┐
├── users/                    │
├── guardians/                │
├── wards/                    ├─ ops-api로 복사
├── calls/                    │
├── rooms/                    │
├── devices/                  │
├── rtc/                      │
├── scheduler/               ─┘
│
└── admin/                   ─┐
    ├── auth/                 │
    ├── dashboard/            ├─ ops-admin-api로 복사 (평탄화)
    ├── emergencies/          │
    ├── locations/            │
    └── wards-management/    ─┘
```

**이제 Phase 3에서 폴더 복사 + 템플릿 적용만으로 레포 생성 가능**

---

## 6. Phase 3: 멀티 레포 분리

### 6.1 목표 레포 구조

| 레포지토리 | 포함 모듈 | 포트 |
|-----------|----------|------|
| `ops-api` | auth, users, guardians, wards, calls, rooms, devices, rtc, push, ai, scheduler | 8080 |
| `ops-admin-api` | admin/* (auth, dashboard, emergencies, locations, wards-management) | 8081 |
| `ops-admin-web` | Next.js 관제 웹 | 3000 |
| `ops-agent` | Python LiveKit Agent | - |
| `ops-shared` | shared/ 폴더 내용 (npm 패키지) | - |

### 6.2 ops-api 구조

```
ops-api/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   │
│   ├── common/
│   ├── prisma/
│   ├── push/
│   ├── ai/
│   ├── scheduler/
│   │
│   ├── auth/
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   └── dto/
│   │
│   ├── users/
│   │   ├── users.module.ts
│   │   ├── users.controller.ts
│   │   ├── users.service.ts
│   │   ├── users.repository.ts
│   │   └── dto/
│   │
│   ├── guardians/
│   ├── wards/
│   ├── calls/
│   ├── rooms/
│   ├── devices/
│   ├── rtc/
│   │
│   ├── internal/
│   │   ├── internal.controller.ts
│   │   └── internal-auth.guard.ts
│   │
│   └── infrastructure/
│       ├── event-bus/
│       └── internal/
│
├── prisma/
│   └── schema.prisma
├── Dockerfile
└── package.json
```

### 6.3 ops-admin-api 구조

```
ops-admin-api/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   │
│   ├── common/
│   ├── prisma/
│   │
│   ├── auth/
│   │   ├── admin-auth.controller.ts
│   │   ├── admin-auth.service.ts
│   │   ├── admin.repository.ts
│   │   └── dto/
│   │
│   ├── dashboard/
│   │   ├── dashboard.controller.ts
│   │   ├── dashboard.service.ts
│   │   ├── dashboard.repository.ts
│   │   └── dto/
│   │
│   ├── emergencies/
│   ├── locations/
│   ├── wards-management/
│   │
│   ├── internal/
│   └── infrastructure/
│
├── prisma/
│   └── schema.prisma
├── Dockerfile
└── package.json
```

### 6.4 ops-shared (npm 패키지)

```
ops-shared/
├── src/
│   ├── types/
│   │   ├── user.types.ts
│   │   ├── ward.types.ts
│   │   ├── call.types.ts
│   │   └── index.ts
│   │
│   ├── events/
│   │   ├── call.events.ts
│   │   ├── user.events.ts
│   │   └── index.ts
│   │
│   ├── dto/
│   │   ├── common.dto.ts
│   │   └── index.ts
│   │
│   └── index.ts
│
├── package.json
├── tsconfig.json
└── .github/
    └── workflows/
        └── publish.yml
```

### 6.5 ops-agent (Python)

```
ops-agent/
├── src/
│   ├── main.py
│   ├── agent.py           # AgentSession 설정
│   ├── assistant.py       # AI 페르소나
│   └── config.py          # 환경변수
│
├── Dockerfile
├── requirements.txt
└── pyproject.toml
```

**agent.py:**

```python
from livekit import agents
from livekit.agents import AgentSession
from livekit.plugins import aws

async def entrypoint(ctx: agents.JobContext):
    session = AgentSession(
        stt=aws.STT(language="ko-KR"),
        llm=aws.LLM(model="anthropic.claude-haiku-4-5-v1:0"),
        tts=aws.TTS(voice="Seoyeon"),
    )
    await session.start(room=ctx.room, agent=Assistant())
```

---

## 7. Phase 4: 인프라 및 배포

### 7.1 로컬 개발 환경

```yaml
# docker-compose.yml
version: '3.8'

services:
  ops-api:
    build: ./ops-api
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://damso:damso@postgres:5432/damso
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis

  ops-admin-api:
    build: ./ops-admin-api
    ports:
      - "8081:8081"
    environment:
      - DATABASE_URL=postgresql://damso:damso@postgres:5432/damso
      - OPS_API_URL=http://ops-api:8080
    depends_on:
      - postgres
      - ops-api

  ops-admin-web:
    build: ./ops-admin-web
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_ADMIN_API_URL=http://localhost:8081

  ops-agent:
    build: ./ops-agent
    environment:
      - LIVEKIT_URL=ws://livekit:7880
    depends_on:
      - livekit

  postgres:
    image: postgres:16-alpine
    environment:
      - POSTGRES_USER=damso
      - POSTGRES_PASSWORD=damso
      - POSTGRES_DB=damso

  redis:
    image: redis:7-alpine

  livekit:
    image: livekit/livekit-server:latest
    ports:
      - "7880:7880"
```

### 7.2 프로덕션 (AWS)

| 서비스 | AWS 리소스 |
|-------|-----------|
| ops-api | ECS Fargate (2-8 tasks) |
| ops-admin-api | ECS Fargate (2-4 tasks) |
| ops-admin-web | ECS Fargate + CloudFront |
| ops-agent | ECS Fargate (2-10 tasks) |
| Database | RDS PostgreSQL (Multi-AZ) |
| Cache | ElastiCache Redis |
| AI | Bedrock (Claude), Transcribe, Polly |

---

## 8. 실행 계획

### 8.1 Phase 1 체크리스트

```
□ Step 1: calls/ 모듈
  □ calls.repository.ts 이동 (database/ → calls/)
  □ calls.service.ts 생성
  □ dto/ 완성 (invite, answer, response, summary)
  □ calls.controller.ts 수정 (AppService → CallsService)
  □ calls.module.ts 업데이트
  □ 빌드 및 테스트

□ Step 2: rooms/ 모듈 (신규)
  □ rooms.repository.ts 이동
  □ rooms.service.ts 생성
  □ rooms.controller.ts 생성 (GET /rooms/:name/members)
  □ dto/ 완성
  □ rooms.module.ts 생성

□ Step 3: devices/ 모듈
  □ devices.repository.ts 이동
  □ devices.service.ts 생성
  □ dto/ 완성
  □ devices.controller.ts 수정
  □ devices.module.ts 업데이트

□ Step 4: users/ 모듈
  □ users.repository.ts 이동
  □ users.service.ts 수정
  □ dto/ 보완

□ Step 5: wards/ 모듈
  □ wards.repository.ts 이동
  □ wards.service.ts 수정
  □ dto/ 완성

□ Step 6: guardians/ 모듈
  □ guardians.repository.ts 이동
  □ guardians.service.ts 수정
  □ dto/ 완성

□ Step 7: auth/ 모듈
  □ auth.service.ts 수정 (UsersRepository 직접 사용)
  □ dto/ 확인

□ Step 8: rtc/ 모듈
  □ rtc.service.ts 생성
  □ dto/ 완성

□ Step 9: admin/* 모듈들
  □ admin/auth - admin.repository.ts 이동
  □ admin/dashboard - dashboard.repository.ts 이동, service 생성
  □ admin/emergencies - emergencies.repository.ts 이동, service 생성
  □ admin/locations - locations.repository.ts 이동, service 생성
  □ admin/wards-management - service 생성

□ Step 10: 정리
  □ shared/ 폴더 생성 및 types 이동
  □ app.service.ts 삭제
  □ database/db.service.ts 삭제
  □ database/repositories/ 폴더 삭제
  □ database/database.module.ts 삭제
  □ 전체 빌드 확인
  □ 전체 테스트
```

### 8.2 Phase 2 체크리스트

```
□ Event Bus 구현
  □ infrastructure/event-bus/ 폴더 생성
  □ RedisEventBus 구현
  □ 이벤트 타입 정의 (shared/events/)
  □ 발행/구독 테스트

□ Internal API
  □ internal/ 폴더 생성
  □ InternalAuthGuard 구현
  □ InternalController 추가
  □ InternalApiClient 구현
```

### 8.3 Phase 2.5 체크리스트

```
□ ops-shared 패키지 추출
  □ packages/ops-shared/ 폴더 생성
  □ shared/types/ → packages/ops-shared/src/types/ 이동
  □ shared/events/ → packages/ops-shared/src/events/ 이동
  □ shared/mappers/ → packages/ops-shared/src/mappers/ 이동
  □ package.json, tsconfig.json 작성
  □ api/에서 import 경로 변경 ('../shared/' → '@ops/shared')
  □ npm link로 로컬 테스트

□ 레포별 템플릿 준비
  □ app.module.ops-api.ts 작성
  □ app.module.ops-admin-api.ts 작성
  □ main.ops-api.ts 작성
  □ main.ops-admin-api.ts 작성

□ admin/ 평탄화 준비
  □ scripts/flatten-admin.sh 스크립트 작성
  □ admin-auth → auth 리네임 규칙 정의
  □ 테스트 실행
```

### 8.4 Phase 3 체크리스트

```
□ ops-shared 레포 생성
  □ shared/ 내용 이동
  □ npm 패키지 설정
  □ publish 워크플로우

□ ops-api 레포 생성
  □ 관련 모듈 복사
  □ admin/ 모듈 제거
  □ CI/CD 설정
  □ 배포 테스트

□ ops-admin-api 레포 생성
  □ admin/ 모듈 이동 및 루트로 재구성
  □ 독립 NestJS 프로젝트 설정
  □ CI/CD 설정
  □ 배포 테스트

□ ops-admin-web 레포 생성
  □ web/ 이동
  □ API URL 환경변수 변경
  □ CI/CD 설정
  □ 배포 테스트

□ ops-agent 레포 생성
  □ Python 프로젝트 생성
  □ AWS 연동 (Transcribe, Bedrock, Polly)
  □ CI/CD 설정
  □ 배포 테스트
```

### 8.5 Phase 4 체크리스트

```
□ DNS 설정
  □ api.example.com → ops-api
  □ admin-api.example.com → ops-admin-api
  □ admin.example.com → ops-admin-web

□ 모니터링 구축
  □ CloudWatch Logs
  □ CloudWatch Alarms

□ 기존 모노레포 아카이브
```

---

## 변경 이력

| 버전 | 날짜 | 내용 |
|------|------|------|
| 2.2.0 | 2025-12-31 | 구현 현황 업데이트 |
| | | - EventsModule 추가됨 (SSE 기반 실시간 로그아웃/탈퇴 알림) |
| | | - rtc/ 모듈: identity 우선순위 변경 완료 (authIdentity 우선) |
| 2.1.0 | 2025-12-31 | Phase 2.5 추가 - 레포 분리 준비 단계 |
| | | - ops-shared npm 패키지 추출 계획 |
| | | - 레포별 app.module.ts, main.ts 템플릿 |
| | | - admin/ 모듈 평탄화 스크립트 |
| | | - Phase 2.5 체크리스트 추가 |
| 2.0.0 | 2025-12-31 | 전면 재작성 - Vertical Slice + 멀티 레포 통합 |
| 1.0.0 | 2025-12-30 | 초안 (Facade 패턴 유지 전제) - 폐기 |
