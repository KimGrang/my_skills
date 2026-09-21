# 스킬 · 플러그인 인벤토리 (공개용)

로컬 개발 환경(WSL + Windows)에 설치된 AI 코딩 에이전트 skill/plugin을 도구별로 정리한 요약. 특정 사용자·프로젝트를 식별할 수 있는 경로·이름·통계는 제외했다.

---

## 1. Claude Code

### 전역 skill (`~/.claude/skills/`)

| skill | 형태 | 호출 | 용도 |
|---|---|---|---|
| `hail-mary-rocky` | 직접 설치 | 자동 (`CLAUDE.md`로 세션마다 켬) | 압축된 Rocky 말투 |
| `eli5` | 공용 에이전트 skill 디렉터리에 대한 symlink | 자동 | 대상 수준에 맞춘 설명. 켜지면 Rocky 말투는 그 답변에서 꺼짐 |
| `archify` | 공용 에이전트 skill 디렉터리에 대한 symlink | 자동 | 아키텍처·시퀀스·상태 다이어그램을 HTML로 생성 |
| `hwp` | 직접 설치 | 자동 | HWP/HWPX 파싱·변환 (kordoc) |
| `find-skills` | 공용 에이전트 skill 디렉터리에 대한 symlink | 자동 | skill 검색·설치 보조 |
| `grill-me` | 직접 설치 (`npx skills`, mattpocock/skills) | 수동 (`/grill-me`) | 계획·설계를 끝까지 캐묻는 인터뷰. 내부에서 `grilling` 호출 |
| `grilling` | 직접 설치 (`npx skills`) | 자동 | 인터뷰 본체. "grill" 표현에도 발동 |
| `domain-modeling` | 직접 설치 (`npx skills`) | 자동 | 용어 정리, `CONTEXT.md`·ADR 작성. 저장소에 파일을 만듦 |
| `writing-for-agents` | 직접 설치 (`npx skills`) | 자동 | skill·`CLAUDE.md` 작성 가이드 |
| `handoff` | 직접 설치 (`npx skills`) | 수동 (`/handoff`) | 대화를 다음 에이전트용 인수인계 문서로 요약 |

`~/.claude/skills/` 안의 `synced/`는 `SKILL.md`가 없는 동기화용 폴더라 skill로 세지 않음.

`grill-me`는 `grilling`이 없으면 동작하지 않으므로 함께 설치해야 함. 같은 저장소의 `wait-what`(직전 답변을 쉬운 영어로 재설명)은 `eli5`로 대체 가능하다고 판단해 설치하지 않음.

### Plugin

| plugin | marketplace | 상태 | 제공 skill | 비고 |
|---|---|---|---|---|
| `superpowers` | `superpowers-dev` (github `obra/superpowers`) | 활성 | brainstorming, writing-plans, executing-plans, test-driven-development, systematic-debugging, verification-before-completion, requesting-code-review, receiving-code-review, subagent-driven-development, dispatching-parallel-agents, using-git-worktrees, finishing-a-development-branch, writing-skills, diagnosing-superpowers, using-superpowers | 세션 시작 시 자동 로드되는 프로세스 skill 모음 |
| `playwright` | `claude-plugins-official` | 활성 | (skill 없음) | 브라우저 자동화 MCP 도구만 제공 |
| `superpowers` | `claude-plugins-official` | 비활성 | (위와 동일, 구버전) | `superpowers-dev` 쪽으로 대체됨 |
| `frontend-design` | `claude-plugins-official` | 비활성 | frontend-design | |
| `mcp-server-dev` | `claude-plugins-official` | 비활성 | build-mcp-app, build-mcp-server, build-mcpb | |

세션 skill 목록에는 위 외에 `anthropic-skills:*`(docs, docx, pdf, pptx, xlsx, skill-creator, import-memory, morning)도 노출됨. 로컬 plugin 설치 목록에는 없는 항목.

### 내장(built-in) skill — plugin/설치 아님

Claude Code 바이너리에 기본 포함되어 있어 파일시스템에 `SKILL.md`로 존재하지 않음: `design`, `dataviz`, `artifact-design`, `artifact-diagramming`, `artifact-capabilities`, `update-config`, `keybindings-help`, `code-review`, `simplify`, `fewer-permission-prompts`, `loop`, `schedule`, `claude-api`, `run`, `init`, `security-review`.

### 프로젝트 스코프 skill

일부 프로젝트에 `<project>/.claude/skills/`로 프로젝트 전용 skill(예: 도메인 모델링/ADR 관리용 skill)을 두는 경우가 있음. 도메인 모델링 skill(`domain-modeling`)은 이제 전역에도 설치됨. **관찰된 패턴**: 동일 프로젝트를 여러 플랫폼(WSL/Windows)에 복제해 두면 두 사본의 로컬 skill 구성이 서로 달라지는 경우가 있음 — 한쪽에만 있고 다른 쪽엔 없는 skill이 존재. 동기화 프로세스가 없다면 흔히 생기는 드리프트.

---

## 2. Codex CLI

### 시스템 기본 skill

`imagegen`, `openai-docs`, `plugin-creator`, `skill-creator`, `skill-installer`는 플랫폼 공통. `review-agent`는 관찰된 환경 중 한쪽(WSL)에만 존재 — 플랫폼별로 시스템 skill 구성이 다를 수 있음을 보여주는 사례.

### 사용자 설치 skill

`hail-mary-rocky` (관찰된 환경 중 한쪽에만 설치됨).

### 공용 에이전트 skill (Codex·Claude Code 공유)

`archify`, `eli5`, `find-skills` — 별도의 공용 skill 디렉터리를 두고 Claude Code/Codex 양쪽에서 symlink 또는 상대경로 참조로 재사용하는 구조.

### plugin 캐시

마켓플레이스에서 내려받은 plugin payload와 임시 체크아웃 사본이 다수 존재. 활성 skill 개수와는 무관한 캐시성 데이터라 상세 수치는 생략.

---

## 3. Cursor

기본 제공 skill: `automate`, `autopilot`, `canvas`, `create-hook`, `create-rule`, `create-skill`, `create-subagent`, `goal`, `loop`, `migrate-to-skills`, `new-repo`, `onboard`, `origin`, `rename-chat`, `review`, `review-bugbot`, `review-security`, `sdk`, `share`, `shell`, `split-to-prs`, `statusline`, `update-cli-config`, `update-cursor-settings`, `visualize`

사용자 정의 skill 디렉터리는 비어 있음 (Cursor 자체, Cline 확장 모두).

---

## 4. VS Code 확장 skill

Codex/Claude Code와 무관하게 Python 관련 확장이 자체적으로 번들.

- Pylance: `pylance-docs`, `pylance-python-profiling`, `pylance-refactoring`, `python-fact-grounded-coding`
- Python Environments: `cross-platform-paths`, `debug-failing-test`, `generate-snapshot`, `python-manager-discovery`, `run-e2e-tests`

---

## 관찰 요약

- 도구별로 skill 관리 방식이 다름: Claude Code는 marketplace plugin + `npx skills` 등으로 설치한 전역 skill, Codex는 시스템 기본 + 사용자 설치 + 공용 디렉터리, Cursor/VS Code 확장은 자체 번들.
- 여러 도구가 같은 공용 skill 디렉터리를 symlink/상대경로로 공유하는 구조가 존재함 (`archify`, `eli5`, `find-skills`, `hail-mary-rocky`).
- 동일 프로젝트를 플랫폼별로 복제 운영할 경우 로컬 skill 구성이 어긋나는(drift) 사례가 관찰됨 — 동기화 관례가 없으면 재현되는 문제.
- Claude Code에는 marketplace plugin 외에 바이너리 내장 skill이 상당수 있어, "설치한 것"과 "기본 제공되는 것"을 구분해서 기록할 필요가 있음.
- 자동 호출 skill끼리 겹치는 영역이 있음: `grill-me`(수동)는 `superpowers:brainstorming`(자동)과 자동으로 부딪히지 않지만, 새 기능 요청에는 brainstorming이 먼저 발동할 가능성이 높아 캐묻기를 원하면 `/grill-me`를 직접 호출해야 함. 코드 리뷰도 내장 `code-review`와 superpowers `requesting-code-review`가 병존.
- 외부 저장소 skill을 설치할 때는 스크립트·SKILL.md를 먼저 읽고 위험 패턴(`curl`, `eval`, 비밀 경로 접근, 외부 전송)을 확인한 뒤 설치하는 절차를 적용함.
