# Tasks: PRD 기반 SoC 아키텍처 탐색 및 PPA 추정

**Input**: Design documents from `/specs/001-prd-based-spec/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED for every behavior change and interface change.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and baseline tooling

- [ ] T001 Create project directory structure in src/ui, src/application, src/domain, src/infrastructure, src/reporting, tests/unit, tests/integration, tests/contract, tests/performance
- [ ] T002 Initialize Python project settings and dependency manifest in pyproject.toml
- [ ] T003 [P] Configure Ruff and formatting rules in pyproject.toml
- [ ] T004 [P] Configure pytest and test markers in pytest.ini
- [ ] T005 [P] Create baseline environment template in .env.example
- [ ] T006 [P] Add canonical UX terminology reference in docs/ux-terms.md
- [ ] T007 [P] Add benchmark scenario matrix for p95/p99 goals in tests/performance/README.md

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T008 Implement shared domain enums and constants in src/domain/value_objects/enums.py
- [ ] T009 Implement RolePolicy and authorization guard in src/domain/policies/role_policy.py
- [ ] T010 Implement deterministic hashing utility for input and candidate digests in src/application/services/determinism_service.py
- [ ] T011 Implement retry orchestration policy (max 3 retries) in src/application/services/retry_policy.py
- [ ] T012 Implement PostgreSQL connection/session factory in src/infrastructure/db/session.py
- [ ] T013 Implement base repository interfaces in src/domain/repositories/base_repository.py
- [ ] T014 Implement audit event logger interface and adapter in src/infrastructure/audit/audit_logger.py
- [ ] T015 Implement global error model and failure code mapping in src/application/errors/failure_codes.py
- [ ] T016 Implement API bootstrap and router wiring for contract v1 in src/infrastructure/api/app.py
- [ ] T017 Add contract validation test harness for OpenAPI schema in tests/contract/test_openapi_contract.py
- [ ] T018 Add CI pipeline gates for lint/unit/integration/contract/performance in .github/workflows/ci.yml

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - 고객 제안용 아키텍처 도출 (Priority: P1) 🎯 MVP

**Goal**: 제품 기획자가 요구사항 입력 후 상위 5개 후보와 PPA 요약을 확인하고 제안 리포트를 내보낸다.

**Independent Test**: 요구사항 1건 입력으로 `/v1/explorations` 실행 후 상위 5개 결과를 받고 `/v1/reports`로 리포트 생성이 완료되어야 한다.

### Tests for User Story 1 (REQUIRED) ⚠️

- [ ] T019 [P] [US1] Add unit tests for requirement normalization and validation in tests/unit/test_requirement_profile.py
- [ ] T020 [P] [US1] Add unit tests for top-5 candidate selection logic in tests/unit/test_top5_selector.py
- [ ] T021 [P] [US1] Add contract tests for POST /v1/explorations in tests/contract/test_create_exploration.py
- [ ] T022 [P] [US1] Add contract tests for POST /v1/reports in tests/contract/test_export_report.py
- [ ] T023 [P] [US1] Add integration test for planner journey (input -> explore -> report) in tests/integration/test_us1_planner_flow.py

### Implementation for User Story 1

- [ ] T024 [P] [US1] Implement RequirementProfile entity model in src/domain/entities/requirement_profile.py
- [ ] T025 [P] [US1] Implement ArchitectureCandidate and PPAEstimate entity models in src/domain/entities/architecture_candidate.py
- [ ] T026 [P] [US1] Implement ResultSnapshot and ProposalReport entity models in src/domain/entities/result_snapshot.py
- [ ] T027 [US1] Implement exploration orchestration service for candidate generation and filtering in src/application/services/exploration_service.py
- [ ] T028 [US1] Implement top-5 ranking and output assembler in src/application/services/top_candidates_service.py
- [ ] T029 [US1] Implement report export service with absolute-value metrics in src/reporting/report_export_service.py
- [ ] T030 [US1] Implement POST /v1/explorations endpoint in src/infrastructure/api/routes/explorations.py
- [ ] T031 [US1] Implement POST /v1/reports endpoint in src/infrastructure/api/routes/reports.py
- [ ] T032 [US1] Implement planner input and result view states (loading/empty/error/success) in src/ui/views/planner_exploration_view.py
- [ ] T033 [US1] Add deterministic snapshot persistence for exploration outputs in src/infrastructure/repositories/result_snapshot_repository.py
- [ ] T034 [US1] Add US1 performance benchmark (200 candidates, p95/p99) in tests/performance/test_us1_latency.py

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently

---

## Phase 4: User Story 2 - 시스템 아키텍트의 트레이드오프 탐색 (Priority: P2)

**Goal**: 시스템 아키텍트가 파라미터를 조정하며 파레토 기반 트레이드오프 비교로 후보 우선순위를 재평가한다.

**Independent Test**: 동일 요구사항에서 파라미터 변경 재실행 후 파레토 클래스와 우선순위 변화를 비교 화면에서 검증할 수 있어야 한다.

### Tests for User Story 2 (REQUIRED) ⚠️

- [ ] T035 [P] [US2] Add unit tests for pareto frontier classification in tests/unit/test_pareto_classifier.py
- [ ] T036 [P] [US2] Add unit tests for deterministic rerun parity (100 runs, mismatch 0) in tests/unit/test_deterministic_replay.py
- [ ] T037 [P] [US2] Add contract tests for GET /v1/explorations/{explorationId} in tests/contract/test_get_exploration_snapshot.py
- [ ] T038 [P] [US2] Add integration test for architect tradeoff workflow in tests/integration/test_us2_tradeoff_flow.py

### Implementation for User Story 2

- [ ] T039 [P] [US2] Implement pareto classification domain service in src/domain/services/pareto_service.py
- [ ] T040 [P] [US2] Implement parameter variation request model in src/domain/entities/parameter_variation.py
- [ ] T041 [US2] Implement tradeoff analysis application service in src/application/services/tradeoff_analysis_service.py
- [ ] T042 [US2] Implement deterministic replay validator service in src/application/services/replay_validator_service.py
- [ ] T043 [US2] Implement GET /v1/explorations/{explorationId} endpoint in src/infrastructure/api/routes/exploration_snapshot.py
- [ ] T044 [US2] Implement architect comparison view states (loading/empty/error/success) in src/ui/views/architect_tradeoff_view.py
- [ ] T045 [US2] Add integration of replay digest check into result retrieval in src/infrastructure/repositories/result_snapshot_repository.py
- [ ] T046 [US2] Add performance and reliability test for retry success rate in tests/performance/test_us2_retry_reliability.py

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently

---

## Phase 5: User Story 3 - IP 오너의 카탈로그/모델 최신화 (Priority: P3)

**Goal**: IP 오너가 카탈로그/모델 버전을 안전하게 갱신하고 충돌을 수동 병합 절차로 통제한다.

**Independent Test**: 동일 IP 항목 동시 수정 요청 시 409 충돌이 반환되고 자동 덮어쓰기가 발생하지 않아야 하며, 업데이트 후 탐색 결과 메타데이터에 버전이 반영되어야 한다.

### Tests for User Story 3 (REQUIRED) ⚠️

- [ ] T047 [P] [US3] Add unit tests for optimistic locking conflict detection in tests/unit/test_catalog_conflict_guard.py
- [ ] T048 [P] [US3] Add contract tests for PUT /v1/catalog/items/{ipItemId} conflict behavior in tests/contract/test_update_catalog_item.py
- [ ] T049 [P] [US3] Add integration test for IP owner update workflow in tests/integration/test_us3_catalog_update_flow.py
- [ ] T050 [P] [US3] Add integration test for conflict blocking and manual-merge requirement in tests/integration/test_us3_conflict_blocking.py

### Implementation for User Story 3

- [ ] T051 [P] [US3] Implement IPCatalogItem and IPVariant entity models with version fields in src/domain/entities/ip_catalog_item.py
- [ ] T052 [P] [US3] Implement PPAModelVersion entity model in src/domain/entities/ppa_model_version.py
- [ ] T053 [US3] Implement catalog update service with optimistic lock checks in src/application/services/catalog_update_service.py
- [ ] T054 [US3] Implement model version registration service in src/application/services/model_version_service.py
- [ ] T055 [US3] Implement PUT /v1/catalog/items/{ipItemId} endpoint with 409 mapping in src/infrastructure/api/routes/catalog_items.py
- [ ] T056 [US3] Implement IP owner catalog management view states (loading/empty/error/success) in src/ui/views/ip_catalog_admin_view.py
- [ ] T057 [US3] Persist catalog/model version lineage into snapshots in src/infrastructure/repositories/catalog_repository.py
- [ ] T058 [US3] Add audit events for model view/update/report export in src/infrastructure/audit/audit_logger.py

**Checkpoint**: All user stories should now be independently functional

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T059 [P] Update architecture and API usage documentation in docs/quickstart.md
- [ ] T060 Refactor duplicated validation logic across services in src/application/services/
- [ ] T061 [P] Expand regression suite for edge cases and failure codes in tests/integration/test_edge_cases.py
- [ ] T062 [P] Run full benchmark suite and capture evidence in tests/performance/benchmark_report.md
- [ ] T063 [P] Run UX consistency review across planner/architect/IP-owner views in src/ui/views/
- [ ] T064 Perform security and role-policy verification for all endpoints in tests/integration/test_rbac_enforcement.py
- [ ] T065 Validate end-to-end quickstart acceptance flow in specs/001-prd-based-spec/quickstart.md

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 -> P2 -> P3)
- **Polish (Phase 6)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Starts after Foundational and defines MVP flow
- **User Story 2 (P2)**: Starts after Foundational, reuses exploration outputs but remains independently testable
- **User Story 3 (P3)**: Starts after Foundational, independent from US2 and focuses on catalog/model governance

### Within Each User Story

- Tests MUST be written and FAIL before implementation
- Models before services
- Services before endpoints/UI wiring
- Performance and reliability evidence before story sign-off
- Story completion validated before moving to next priority

### Parallel Opportunities

- Setup tasks marked [P] can run in parallel
- Foundational tasks T009/T010/T011/T014 can run in parallel after T008
- Within US1, T019-T023 and T024-T026 are parallelizable
- Within US2, T035-T038 and T039-T040 are parallelizable
- Within US3, T047-T050 and T051-T052 are parallelizable

---

## Parallel Example: User Story 1

```bash
Task: "T019 [US1] Add unit tests for requirement normalization in tests/unit/test_requirement_profile.py"
Task: "T021 [US1] Add contract tests for POST /v1/explorations in tests/contract/test_create_exploration.py"
Task: "T024 [US1] Implement RequirementProfile entity model in src/domain/entities/requirement_profile.py"
Task: "T025 [US1] Implement ArchitectureCandidate and PPAEstimate entity models in src/domain/entities/architecture_candidate.py"
```

## Parallel Example: User Story 2

```bash
Task: "T035 [US2] Add unit tests for pareto frontier classification in tests/unit/test_pareto_classifier.py"
Task: "T037 [US2] Add contract tests for GET /v1/explorations/{explorationId} in tests/contract/test_get_exploration_snapshot.py"
Task: "T039 [US2] Implement pareto classification domain service in src/domain/services/pareto_service.py"
Task: "T040 [US2] Implement parameter variation request model in src/domain/entities/parameter_variation.py"
```

## Parallel Example: User Story 3

```bash
Task: "T047 [US3] Add unit tests for optimistic locking conflict detection in tests/unit/test_catalog_conflict_guard.py"
Task: "T048 [US3] Add contract tests for PUT /v1/catalog/items/{ipItemId} in tests/contract/test_update_catalog_item.py"
Task: "T051 [US3] Implement IPCatalogItem and IPVariant entity models in src/domain/entities/ip_catalog_item.py"
Task: "T052 [US3] Implement PPAModelVersion entity model in src/domain/entities/ppa_model_version.py"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Complete Phase 3: User Story 1
4. Validate SC-001, SC-002, and top-5 reporting flow
5. Demo planner workflow as MVP

### Incremental Delivery

1. Setup + Foundational complete
2. Deliver US1 (planner proposal flow)
3. Deliver US2 (architect tradeoff + deterministic replay validation)
4. Deliver US3 (catalog/model governance + conflict blocking)
5. Finish polish with full regression/performance evidence

### Parallel Team Strategy

1. Team completes Setup + Foundational together
2. After Foundational:
   - Developer A: US1 endpoints/reporting
   - Developer B: US2 pareto/replay
   - Developer C: US3 catalog/version/conflict
3. Converge in Phase 6 for cross-cutting validation

---

## Notes

- [P] tasks = different files, no dependencies
- [USx] labels map tasks to story-specific delivery slices
- Every story has independent tests and implementation tasks
- All task descriptions include concrete file paths
- Format validated: all tasks follow `- [ ] Txxx [P?] [US?] Description with file path`
