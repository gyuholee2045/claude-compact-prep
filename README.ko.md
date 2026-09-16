# compact-prep

[English](README.md) | **한국어**

Claude Code 세션이 압축(`/compact`)되기 전에 이번 대화의 결정과 스펙을 파일로 옮겨두고, 압축 후에 그 파일로 맥락을 되살리는 스킬입니다.

> A Claude Code skill that writes your session's decisions, exact specs, numbering, rejected alternatives, design language, and working preferences into durable project files before `/compact`, then restores context from them afterwards. Korean-language skill body.

## 왜 필요한가

`/compact`의 자동 요약은 손실 압축입니다. 숫자, 고유 명칭, 검토하다 버린 대안, 사용자가 중간에 한 교정처럼 "다시 물어보면 티 나는" 정보가 특히 잘 사라집니다. 압축 후 세션이 같은 결정을 처음부터 다시 묻거나, 확정했던 값을 다르게 쓰는 일이 여기서 생깁니다.

이 스킬의 기준은 하나입니다. **기록 파일만 읽은 새 세션이 같은 결정을 다시 묻지 않고, 같은 숫자를 다르게 쓰지 않는다.**

## 설치

스킬 디렉터리에 폴더째 넣으면 됩니다.

```bash
git clone https://github.com/gyuholee2045/claude-compact-prep.git ~/.claude/skills/compact-prep
```

Windows (PowerShell):

```powershell
git clone https://github.com/gyuholee2045/claude-compact-prep.git "$env:USERPROFILE\.claude\skills\compact-prep"
```

프로젝트 하나에만 쓰려면 `~/.claude/skills/` 대신 그 프로젝트의 `.claude/skills/`에 넣으세요. 설치 후에는 세션을 새로 열어야 목록에 잡힙니다.

## 사용법

| 상황 | 입력 |
|---|---|
| 압축 직전 저장 | `/compact-prep` |
| 저장하면서 다음 세션 방향도 남기기 | `/compact-prep 다음엔 섹션 4 카피부터` |
| 압축 후 복구하고 이어서 작업 | `/compact-prep restore` |

슬래시 명령 없이 "압축 전에 저장해줘", "컴팩트 준비", "압축 끝났어 이어서 하자"처럼 말해도 발동합니다.

## 무엇이 저장되나

작업 폴더에 `session-memory/`를 만들고 범주별로 나눠 씁니다. 해당 내용이 없는 파일은 만들지 않습니다.

| 파일 | 내용 |
|---|---|
| `PROGRESS.md` | 현재 상태, 진행 중인 작업과 다음 한 걸음, 막힌 것, 건드린 파일. 복구의 입구 |
| `DECISIONS.md` | 무엇을 왜 정했나, 검토하고 버린 대안과 버린 이유, 같은 대상이 v1→v2→v3로 바뀐 이력 |
| `SPECS.md` | 숫자, 색상 코드, 폰트, 버전, 경로, 확정 카피 원문, 넘버링 체계 |
| `DESIGN.md` | 톤, 레이아웃 원칙, 타이포·색·간격 규칙, 레퍼런스, 하지 말 것, 시안 위치 |
| `STYLE.md` | 작업 방식 선호, 싫어하는 것, 사용자 교정 발언 인용 |

## 기록 규칙

정밀도가 이 스킬의 전부입니다. 요약이 뭉개는 것들을 여기서 살립니다.

- **누적.** 기존 파일을 먼저 읽고 그 위에 더합니다. 덮어쓰지 않습니다.
- **원문 그대로.** `약 24px`, `파란 계열`로 뭉개지 않고 대화에 나온 값을 그대로 옮깁니다.
- **넘버링 보존.** 사용자가 쓴 번호 체계는 표기 그대로 적고, 스킬이 붙이는 `D-`/`S-` ID는 한 번 붙이면 바꾸지 않습니다. 다른 기록이 그 번호로 서로를 가리키기 때문입니다.
- **뒤집힌 결정은 삭제 대신 대체 표시.** `~~D-004~~ → D-011로 대체`. 뭐가 왜 바뀌었는지가 곧 고민의 흔적입니다.
- **불확실하면 표시.** 값이 두 번 다르게 나왔으면 추측해서 고르지 않고 `[확인 필요]`로 남기고 물어봅니다.
- **대화 전체를 훑습니다.** 최근 몇 턴이 아니라 처음부터. 초반 결정일수록 요약에서 먼저 사라집니다.
- **비밀값은 적지 않습니다.** 키나 토큰은 값 대신 위치만 남깁니다.

## 복구를 자동으로 만들려면

프로젝트 `CLAUDE.md`에 한 줄 넣어두면 `restore`를 입력하지 않아도 됩니다. `CLAUDE.md`는 압축 후에도 다시 로드되기 때문입니다.

```markdown
- 세션 시작·압축 직후에는 session-memory/PROGRESS.md부터 읽고 기록된 결정과 스펙을 따른다.
```

스킬이 어떤 프로젝트에서 처음 저장할 때 이 줄을 추가할지 물어봅니다. 동의할 때만 넣습니다.

## 압축 명령과 함께 쓰기

저장이 끝나면 스킬이 붙여 넣을 수 있는 압축 명령을 제안합니다. 요약이 파일을 가리키게 해두면 복구가 쉬워집니다.

```
/compact 결정·스펙은 session-memory/에 기록됨. 요약 시 D-/S- 번호와 파일 경로를 그대로 유지하고, 진행 중 작업과 다음 할 일을 우선 보존할 것.
```

## 라이선스

MIT
