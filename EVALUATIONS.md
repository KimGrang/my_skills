# 도입 검토 기록

외부 skill·plugin·도구 후보를 검토하고 내린 결정. 다시 조사하지 않도록 판단 근거와 재검토 조건을 함께 남긴다.

- **수요 없음**: 지금 쓸 일이 없음. 재검토 조건이 생기면 다시 본다.
- **보류**: 쓸모는 있으나 비용·위험·성숙도 때문에 미룸.

## 2026-10-27

README·GitHub API 메타데이터 기준, 설치·실행은 안 함.

| 저장소 | 결정 | 이유 | 재검토 조건 |
|---|---|---|---|
| [trailhq/Trail-Code](https://github.com/trailhq/Trail-Code) | 보류 | tree-sitter 코드 graph("graft map") + 대화에서 뽑은 learning을 `.trail/`에 커밋해 공유(MIT, npm, 2026-07 생성). `trail init`이 user 전역 `~/.claude/settings.json`에 hook 5종(SessionStart·UserPromptSubmit·PreToolUse·PostToolUse·Stop)과 statusline, `~/.claude/helpers/`에 shim을 씀 — 모든 프로젝트 매 prompt에 context 주입. init 때 Claude Code transcript를 Sonnet으로 읽고(내 사용량 차감) `gh` 로그인 시 최근 PR 50개 리뷰도 읽음. telemetry 기본 ON. 벤치마크(비용 −32%, SWE-bench 33/50 vs 27/50)는 저자 자체 측정. graphify·codebase-memory-mcp와 기능 겹침 | 대형 코드베이스에서 탐색 비용이 실제 병목일 때. 그 저장소에서만 `DO_NOT_TRACK=1`로 시험, 전역 hook은 `trail uninstall --dry-run`류로 쓰는 파일 먼저 확인. 제거 `trail uninstall -y` |
| [DeusData/codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) | 보류 (코드 graph 1순위 후보) | 단일 C 바이너리 코드 knowledge graph MCP(MIT, tool 17개, grammar 162개, 로컬 embedding). telemetry 없음, release마다 SHA-256 `checksums.txt`. 단 권장 설치가 `curl \| bash`이고, 기본 `install`이 감지한 모든 agent에 설정·skill·profile·hook을 씀. 계정당 공유 daemon 상시 기동, `auto_watch` 기본 true로 git 프로젝트 자동 재색인. CBM Lens는 v0.0.1 beta. 작은 저장소엔 `code-explorer`로 충분 | 처음 보는 대형 코드베이스 분석 때. release 바이너리 수동 다운로드 → checksum 확인 → `--skip-config`로 바이너리만 설치 → 해당 프로젝트 `.mcp.json`에만 등록. 제거 `codebase-memory-mcp uninstall --dry-run` 후 `-y` |
| [calesthio/OpenMontage](https://github.com/calesthio/OpenMontage) | 수요 없음 | agent 주도 영상 제작 저장소(AGPL-3.0, pipeline 12개, skill·지식 파일 700+). skill/plugin이 아니라 clone 후 그 안에서 agent를 돌리는 독립 프로젝트라 전역 오염은 없음. Python·Node·FFmpeg 필요, 고품질은 fal·Kling·ElevenLabs 등 유료 key(영상당 ~$1–3). hyperframes와 같은 범주 | 영상 제작이 필요해질 때 (hyperframes와 비교) |
| [msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents) | 수요 없음 (유지) | 2026-10-21 검토 이후 변경은 agent 추가(Electrical Engineer)뿐. 판단 그대로 | 2026-10-21 항목과 같음 |

`trail-code`는 `trailhq/Trail-Code`로 보고 검토했다.

## 2026-10-26

| 저장소 | 결정 | 이유 | 재검토 조건 |
|---|---|---|---|
| [AgriciDaniel/claude-seo](https://github.com/AgriciDaniel/claude-seo) | 보류 (조건부 도입) | SEO 감사 plugin(MIT, skill 26개·agent 19개·`/seo`). Python 3.11–3.14 필요, `/seo setup`이 별도 Python 환경과 headless Chromium 설치. 기본 기능은 API key 불필요(Google 연동은 선택). 감사 대상 페이지 내용이 Claude로 전송되고 Wikipedia·Reddit·Common Crawl·Moz·Bing 등 공개 출처 조회. `hooks.json`의 PostToolUse(`Edit\|Write`, 30초) 훅이 켜져 있으면 모든 프로젝트의 모든 편집 뒤 `validate-schema.py` 실행. skill·agent 설명이 매 세션 context에 실려 CLAUDE.md skill 라우팅과 충돌 가능. 수동 `install.ps1`은 `~\.claude\skills`·`agents`에 묻지 않고 덮어쓰고, 오류 일부 무시, 체크섬 없이 `runtime.py` 실행(git 태그 v2.5.0만 고정) — 비추천 | 운영 중인 웹사이트 SEO 작업 계획이 생길 때. `/plugin marketplace add AgriciDaniel/claude-seo` → `/plugin install claude-seo@agricidaniel-claude-seo`를 해당 웹 프로젝트(예: dodaccare_web)의 project/local 범위로만, user 전역 금지. 설치 전 `scripts/runtime.py`·`requirements.txt` 검토(pip/uv 대상, Chromium 출처). 제거는 `/plugin uninstall`, `/seo setup`이 만든 Python 환경·Chromium은 따로 삭제 |

## 2026-10-21

외부 "GitHub 인기 저장소 Top 10" 목록 검토. README·GitHub API 메타데이터 기준, 설치·실행은 안 함.

| 저장소 | 결정 | 이유 | 재검토 조건 |
|---|---|---|---|
| [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | 보류 (1순위 후보) | 6단계 다중 agent 보안 감사 skill(MIT, `npx skills add`). 내장 `security-review`(diff 단위)와 달리 저장소 전체 대상이고 발견마다 독립 verifier가 반증 시도. 대상 코드 실행엔 네트워크 차단 OS sandbox 필요, 없으면 `needs_validation`에 머묾. subagent 다수라 실행당 토큰 큼 | 공개 배포할 서비스·라이브러리 감사가 필요할 때. 기본 출력 경로 `~/security-audit-skill/<repo>/` 확인 |
| [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) | 보류 | "최소 코드" 상시 프롬프트 plugin(MIT). hook·band(UI)·`~/.config/ponytail/` 세션 기록 포함. 매 답변 끝 "생략·미확인 항목" 형식이 Rocky 말투와 겹치고, `/ponytail-review`·`-audit`이 내장 `code-review`와 겹침. 벤치마크는 저자 자체 측정 | 에이전트가 과잉 구현하는 문제가 실제로 보일 때. 한 프로젝트에서 `/ponytail band off`로 시험 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | 보류 | 코드베이스 → knowledge graph `/graphify` skill(Apache-2.0). 코드는 tree-sitter AST로 로컬 처리, 문서·PDF semantic pass만 모델 호출. README에 Cloud 유료 유도 많음. 작은 저장소엔 `code-explorer`로 충분 | 처음 보는 대형 코드베이스·monorepo 분석 때 `graphify install --project`로 그 저장소에만 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | 보류 | tool 출력·로그 압축 proxy/MCP(Apache-2.0). `headroom wrap claude`가 모든 API 트래픽을 로컬 proxy로 돌리고 Serena MCP를 `~/.claude.json`에 등록. telemetry beacon 기본 ON. 코딩 agent 절감 주장 ~20% | 토큰 비용이 실제 병목이 될 때. `HEADROOM_TELEMETRY=off`, `--code-memory none`으로 한 프로젝트 측정 |
| [ruvnet/ruflo](https://github.com/ruvnet/ruflo) | 수요 없음 | multi-agent swarm harness. 전체 설치(`npx ruflo init`)는 agent 98개·MCP 도구 314개·hook·daemon·`CLAUDE.md` 생성으로 context hygiene 원칙과 정면 충돌. mods는 sandbox 없이 실행 | 다중 agent 상시 운영 수요 (openrig와 함께 비교) |
| [msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents) | 수요 없음 | persona subagent 대량 라이브러리(MIT). 일괄 설치 시 agent 목록이 커져 매 세션 context를 차지하고, 개발 작업엔 superpowers·`code-explorer`로 충분 | 특정 역할 agent가 필요할 때 해당 `.md` 하나만 검토 후 복사 |
| [stablyai/orca](https://github.com/stablyai/orca) | 수요 없음 | 여러 coding agent를 worktree별로 병렬 실행하는 데스크톱 앱(MIT). 1인 순차 작업 위주, `using-git-worktrees`로 충분 | 같은 작업을 여러 agent에 동시에 돌려 비교할 일이 잦아질 때 |
| [anomalyco/opencode](https://github.com/anomalyco/opencode) | 수요 없음 | 오픈소스 coding agent(Claude Code 대체재). skill·plugin이 아님 | 다른 모델 공급자를 주력으로 쓸 때 |
| [Comfy-Org/ComfyUI](https://github.com/Comfy-Org/ComfyUI) | 수요 없음 | diffusion 이미지 생성 노드 GUI(GPL-3.0). agent 도구 아님, 로컬 GPU 필요 | 이미지 생성 작업이 생길 때 |
| [MadsLorentzen/ai-job-search](https://github.com/MadsLorentzen/ai-job-search) | 수요 없음 | Claude Code 기반 구직 framework(MIT). fork해서 쓰는 저장소, LaTeX·bun 필요. fork는 공개라 개인정보 주의(README 경고) | 구직 시작 시. fork 대신 clone 후 private 저장소로 |

## 2026-10-19

[mattpocock/skills](https://github.com/mattpocock/skills) v1.3.1 (`49dd158`) 재검토. 설치·갱신분은 SKILLS.claude.md §1.1.

| skill | 결정 | 이유 | 재검토 조건 |
|---|---|---|---|
| `tdd`, `diagnosing-bugs` | 설치 안 함, 규칙만 반영 | superpowers `test-driven-development`·`systematic-debugging`과 trigger 겹침. 차별점(seam 합의, 독립 기대값, red feedback loop 우선)만 `~/.claude/CLAUDE.md`에 2줄 | superpowers 제거 시 |
| `code-review`, `to-spec`, `to-tickets`, `implement(-spec)`, `wayfinder`, `triage` | 수요 없음 | superpowers 워크플로·`codex-review`와 겹치거나 issue tracker 연동 전제 | 팀 단위 issue 기반 작업 시작 시 |
| `setup-matt-pocock-skills`, `ask-matt`, `research`, `teach` | 수요 없음 | 저자 전용 설정·메타 skill이거나 내장 기능과 겹침 | — |
| `migrate-to-shoehorn`, `scaffold-exercises`, `in-progress/*` | 수요 없음 | 저자 개인 프로젝트용 또는 미완성 | 안정 버전 출시 시 (`in-progress`) |
| `wizard`, `git-guardrails-claude-code`, `retro`, `to-questionnaire` | 보류 | 쓸모 있으나 이번 범위 밖 | 필요 생길 때 개별 검토 |

## 2026-10-08

| 저장소 | 결정 | 이유 | 재검토 조건 |
|---|---|---|---|
| [heygen-com/hyperframes](https://github.com/heygen-com/hyperframes) | 수요 없음 | HTML/CSS로 결정적 MP4 렌더링하는 공식 Claude Code plugin(skill 21개). 설치는 쉽지만 영상 제작 수요가 없고, headless browser·ffmpeg 의존성과 skill 목록 증가만 생김 | 제품·PR 소개 영상 제작이 필요해질 때 |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | 수요 없음 | 벡터 없는 tree 기반 RAG(Python 라이브러리, MIT). 긴 PDF에 강하지만 skill이 아니라 직접 감싸야 하고, quickstart가 OpenAI key 기준 | 긴 금융·법률·매뉴얼 문서 질의 업무가 생길 때 (`hwp` skill과 조합 검토) |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | 보류 | 세션 간 자동 memory(hook 5종 + Bun worker + SQLite/Chroma). 내장 memory와 겹치고, 관찰마다 AI 압축 호출로 토큰 비용이 늘며, 자동 주입이 context hygiene 원칙과 충돌. 설치 기본값이 hosted(CMEM Pro) 로그인 유도 | 내장 memory로 부족함이 확인될 때. 시험 시 `--provider` 명시 또는 `CLAUDE_MEM_ONLINE_OPTIN=false`로 로컬 전용, 한 프로젝트에서 토큰 사용량 측정 후 결정 |
| [tester-army/e2e](https://github.com/tester-army/e2e) | 보류 | 자연어 e2e 테스트, 기록 후 모델 호출 없이 replay. 전역 skill이 아니라 프로젝트 단위 도구이고 pre-1.0, telemetry 기본 ON. 기존 Playwright plugin과 겹침 | 실제 웹앱 프로젝트에서 e2e가 필요할 때 그 저장소에 `npx e2e init` (`E2E_TELEMETRY_DISABLED=1`) |
| [NVIDIA/OpenShell](https://github.com/NVIDIA/OpenShell) | 보류 | 커널 수준 정책 강제 agent sandbox. Docker/Podman 필요, WSL2 지원 experimental, v0.1.x, telemetry 수집. 1인 로컬 환경에는 과함 | 무인·장시간 자율 agent 운영이 필요하거나 WSL2 지원이 안정화될 때 |
| [mvschwarz/openrig](https://github.com/mvschwarz/openrig) | 보류 | Claude Code + Codex agent 팀을 YAML로 정의해 tmux에서 상시 실행. 2026-04 생성(v0.6.6)으로 초기 단계이고, workshop bundle이 permission prompt를 우회. subagent·`codex-review`와 겹침 | 다중 agent 상시 운영 수요가 생길 때. 격리된 디렉터리에서 permission 우회를 끄고 시험 |

요청 목록의 `mvschwarz/openring`은 존재하지 않아 같은 저자의 `openrig`로 보고 검토했다.
