# 도입 검토 기록

외부 skill·plugin·도구 후보를 검토하고 내린 결정. 다시 조사하지 않도록 판단 근거와 재검토 조건을 함께 남긴다.

- **수요 없음**: 지금 쓸 일이 없음. 재검토 조건이 생기면 다시 본다.
- **보류**: 쓸모는 있으나 비용·위험·성숙도 때문에 미룸.

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
