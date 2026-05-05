# hyeokjun kim

devops engineer / cloud-native bootcamp (2025.09 — 2026.04)

[![open to work](https://img.shields.io/badge/open_to_work-2026_Q1-success)]()
[![AWS SAA](https://img.shields.io/badge/AWS_SAA-in_progress-orange)]()
[![blog](https://img.shields.io/badge/blog-resskim--io-blue)](https://resskim-io.github.io/my-blog)

---

## about

32살, 비전공자. 백엔드 1.2년 (Go·Java·Python)을 거쳐 DevOps로 전환했습니다.
부트캠프 마지막 학기를 보내는 중이고, 신입이지만 3년차 수준의 인프라 자립을 목표로 합니다.
부산에서 매일 달리고, 우분투 홈서버를 굴려 협업/실험 환경으로 씁니다.

**primary spike** — Kubernetes 운영 + Multi-cloud Observability
인프라·GitOps·서비스 메시·관측성 4영역 중 **관측성과 멀티클라우드 운영**에서 가장 깊게 들어갑니다.

---

## featured — goti

> KBO 야구 티켓팅 / 16주 / 6인 팀 / EKS + GKE Active-Active

10만 동접을 가정한 부하 시나리오로 설계했습니다.
실제로 10만 트래픽을 운영해본 것은 아니고, k6로 시나리오 검증한 수치입니다.

| 지표 | 결과 | 측정 구간 |
|---|---|---|
| 동접 검증 | 10만 | k6 ramp-up + soak |
| 5xx | 0.0x% | 부하 테스트 구간 1주 평균 |
| MSA 분리 | 6/6 무중단 | ArgoCD ApplicationSet |
| 배포 전 P0 보안 | 6건 차단 | Trivy + AI 멀티 리뷰 |
| 관측 패널 | 380개 | 그중 매주 들여다보는 건 12개 |

**내가 한 일**
- 6 MSA를 ArgoCD ApplicationSet으로 GitOps 통합
- Istio mTLS · AuthorizationPolicy · Canary 배포 파이프라인
- LGTM 스택 + OTel + Alloy 구성, 운영 중 이상 4건 자동 탐지
- Terraform 기반 멀티클라우드 IaC, ESO로 시크릿 관리

**지금이라면 다르게 할 것**
- 380패널은 SLO 중심으로 절반 줄이기
- ApplicationSet은 유지, Crossplane 조합으로 한 단계 위 추상화 검토

📂 [Goti-k8s] · [Goti-Terraform] · [Goti-monitoring] · [Goti-guardrail-server]

---

## featured — weAlist + internal ops console

8개 마이크로서비스 칸반 협업 플랫폼. EKS + Istio Ambient + ArgoCD GitOps. 4인 팀의 인프라 총괄 (2025.09 — 2026.01).

플랫폼에서 바꾼 것
- Docker Compose → Kind → EKS 마이그레이션 리드
- CI 빌드 시간 **15분 → 3분** (QEMU 에뮬레이션 제거)
- ArgoCD 부트스트랩 순환 의존성을 Terraform 레이어 분리로 해결
- Istio Ambient → Sidecar 전환 (운영 성숙도 우선 판단)

### internal ops console (Go + Gin)

ArgoCD CLI / kubectl을 직접 쓸 때 생기는 "누가 무엇을 했는지" 추적 부재 문제를 해결한 운영자 콘솔.
IDP는 아니고, 4가지 mutating action에 audit log를 자동으로 붙인 단일 운영 평면.
```
[ operator ]
                     │
                     ▼
        ┌───────────────────────────┐
        │   ops portal (Go + Gin)   │
        │   - portal RBAC (3 roles) │
        │   - audit_logs (RDB)      │
        └─────┬───────────┬─────────┘
              │           │
              ▼           ▼
        [ ArgoCD API ] [ K8s API ]
          sync / RBAC    ConfigMap
                            patch
```
**4가지 액션** — ArgoCD sync · ArgoCD RBAC ConfigMap 패치 · 포털 사용자 초대/권한 · 앱 설정 변경.

**왜 ArgoCD 자체 UI를 안 쓰고 wrapper를 만들었나** — ArgoCD UI는 K8s 권한이 곧 ArgoCD 권한이라 RBAC 분리가 어렵고, sync 액터 추적이 안 됩니다. 자체 RBAC와 audit log를 위에 얹은 이유.

---

## developer enablement

부트캠프와 팀 동료를 대상으로 백엔드 엔지니어 교육 시리즈를 진행했습니다.
슬라이드와 발표 대본을 본인이 작성, 각 1시간 분량으로 라이브 발표했습니다.

**MSA란? — 전환 이유와 주의점** (32 슬라이드)
모놀리식 vs MSA 스케일링 비교, DB 분리 전략 매트릭스 (트래픽·도메인·규제 기반), 안티패턴 — 언제 MSA를 쓰지 말아야 하는가.

**Kafka & 비동기 처리 — Event-Driven for Backend Engineers** (41 슬라이드)
MSA에서 Kafka의 4가지 역할 분해 — 트래픽 격리·데이터 동기화·비동기 후속처리·감사 추적.

지금이라면 운영 관점(토픽 retention, 파티션 리밸런싱, lag 모니터링, K8s 위 Strimzi vs MSK)을 한 챕터 더 넣었을 것 같습니다.

📂 〈슬라이드 공개 위치〉

---

## ress-claude-agents

Claude Code 기반 개발 워크플로우 룰셋. 글로벌 `~/.claude` 설치 기준.

EXPLORE → PLAN → IMPLEMENT 패턴, Conventional Commits 강제, K8s 안전 가드 등 개인 개발 규칙을 코드로 강제합니다. weAlist를 포함한 여러 프로젝트에서 동일 룰 스타일로 작업했습니다.

AI 활용 방식의 분리 — *AI가 한 것* 은 보일러플레이트·검색·초안·멀티 리뷰. *본인이 판단한 것* 은 도구 선택·트레이드오프·운영 결정·임계값.

📂 [ress-claude-agents]

---

## stack
