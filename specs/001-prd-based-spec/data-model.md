# Data Model: PRD 기반 SoC 아키텍처 탐색 및 PPA 추정

## 1. RequirementProfile
- Purpose: 탐색 실행의 입력 원본과 정규화 결과를 보관한다.
- Key fields:
  - `requirement_id` (UUID, PK)
  - `segment` (enum: MEMORY, MOBILE, PHYSICAL_AI)
  - `process_node` (string)
  - `power_budget_w` (decimal, >0)
  - `area_budget_mm2` (decimal, >0)
  - `memory_config` (json)
  - `workload_model` (json)
  - `input_hash` (string, unique with version tuple)
  - `created_at_utc` (timestamp)
- Validation rules:
  - 예산/대역폭 관련 수치는 음수 불가.
  - 필수 입력 누락 시 탐색 시작 불가.

## 2. IPCatalogItem
- Purpose: 후보 생성에 사용되는 IP 블록과 버전/변형 메타데이터를 관리한다.
- Key fields:
  - `ip_item_id` (UUID, PK)
  - `ip_type` (enum: CPU, NPU, NOC, MEM_CTRL, PHY)
  - `owner_team` (string)
  - `compatibility_tags` (array<string>)
  - `status` (enum: DRAFT, ACTIVE, DEPRECATED)
  - `updated_at_utc` (timestamp)
  - `version` (integer, optimistic lock)
- Validation rules:
  - `ip_type`와 `compatibility_tags` 조합은 빈값 불가.
  - 동시 수정 시 `version` 불일치면 저장 차단.
- State transitions:
  - DRAFT -> ACTIVE -> DEPRECATED

## 3. IPVariant
- Purpose: 동일 IP의 파라미터 변형을 관리한다.
- Key fields:
  - `variant_id` (UUID, PK)
  - `ip_item_id` (FK -> IPCatalogItem)
  - `variant_name` (string)
  - `parameters` (json)
  - `quality_grade` (enum: EXPERIMENTAL, QUALIFIED, GOLDEN)
- Validation rules:
  - 동일 `ip_item_id` 내 `variant_name` unique.

## 4. PPAModelVersion
- Purpose: 성능/전력/면적 추정 모델 버전과 유효 범위를 추적한다.
- Key fields:
  - `ppa_model_version` (string, PK)
  - `rule_set_version` (string)
  - `segment_scope` (array<segment>)
  - `effective_from_utc` (timestamp)
  - `status` (enum: ACTIVE, RETIRED)
- Validation rules:
  - ACTIVE 모델은 segment별 최소 1개 이상 존재.

## 5. ArchitectureCandidate
- Purpose: 탐색 엔진이 생성한 SoC 후보와 유효성 상태를 저장한다.
- Key fields:
  - `candidate_id` (UUID, PK)
  - `exploration_id` (UUID)
  - `composition` (json)
  - `validity_status` (enum: VALID, INVALID)
  - `invalid_reason_code` (string, nullable)
  - `rank_score` (decimal)
- Validation rules:
  - VALID 후보는 `invalid_reason_code` null.
  - INVALID 후보는 `invalid_reason_code` 필수.

## 6. PPAEstimate
- Purpose: 후보별 PPA 계산 결과를 관리한다.
- Key fields:
  - `estimate_id` (UUID, PK)
  - `candidate_id` (FK -> ArchitectureCandidate)
  - `performance_score` (decimal)
  - `power_w` (decimal)
  - `area_mm2` (decimal)
  - `pareto_class` (enum: NON_DOMINATED, DOMINATED)
- Validation rules:
  - 수치 필드는 음수 불가.

## 7. ResultSnapshot
- Purpose: 재현성 검증을 위한 실행 메타데이터와 결과 요약을 고정 저장한다.
- Key fields:
  - `snapshot_id` (UUID, PK)
  - `exploration_id` (UUID, unique)
  - `input_hash` (string)
  - `ppa_model_version` (FK -> PPAModelVersion)
  - `rule_set_version` (string)
  - `catalog_version` (string)
  - `candidate_digest` (string)
  - `created_by_role` (enum: SYSTEM_ARCHITECT, PRODUCT_PLANNER, IP_OWNER)
  - `created_at_utc` (timestamp)
- Validation rules:
  - 동일 `input_hash + ppa_model_version + rule_set_version + catalog_version` 실행 결과의 `candidate_digest`는 완전 동일해야 한다.

## 8. ProposalReport
- Purpose: 사용자에게 제공되는 최종 제안 결과물.
- Key fields:
  - `report_id` (UUID, PK)
  - `exploration_id` (UUID)
  - `top_candidates` (array<candidate_id>, size=5)
  - `export_format` (enum: PDF, JSON)
  - `created_at_utc` (timestamp)
- Validation rules:
  - `top_candidates`는 정확히 5개.
  - 보고서에는 절대값 PPA 수치 포함(마스킹 없음).

## 9. AuditEvent
- Purpose: 권한 및 데이터 접근 추적.
- Key fields:
  - `event_id` (UUID, PK)
  - `actor_role` (enum)
  - `event_type` (enum: LOGIN, AUTHZ_CHANGE, MODEL_VIEW, MODEL_UPDATE, REPORT_EXPORT)
  - `target_id` (string)
  - `event_time_utc` (timestamp)

## Relationships Summary
- RequirementProfile 1:N ArchitectureCandidate
- ArchitectureCandidate 1:1 PPAEstimate
- Exploration(ResultSnapshot) 1:N ArchitectureCandidate
- IPCatalogItem 1:N IPVariant
- PPAModelVersion 1:N ResultSnapshot
- ResultSnapshot 1:1 ProposalReport

## Data Volume & Scale Assumptions
- 탐색 1회당 후보 최대 200개 생성.
- 기본 노출/내보내기 후보는 상위 5개.
- 동시 사용자 20명 기준 일일 탐색 수요를 처리.
