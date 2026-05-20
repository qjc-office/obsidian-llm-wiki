[English](./README.en.md) | 한국어

# Inspool Wiki for Obsidian

먼저 [Andrej Karpathy의 "LLM Wiki" 원문](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)에 감사드립니다. 이 프로젝트는 그 아이디어에서 직접 영감을 받았습니다.

Karpathy가 제안한 핵심 사상은 단순하면서도 강력합니다: LLM에게 매번 원본 문서를 다시 읽고 일회성 답변을 만들게 하는 대신, LLM이 당신과 원본 자료 사이에서 지속적으로 진화하는 wiki를 유지·관리하게 한다는 것입니다.

Inspool Wiki는 그 아이디어를 Obsidian 친화적인 워크플로우로 구현한 것입니다. 명확한 디렉토리 구조, 에이전트 규칙, 검토 게이트, 그리고 구체적인 ingest 생명주기를 갖추고 있습니다.

## 이 프로젝트가 해결하는 문제

대부분의 사람들은 아직도 LLM으로 문서 작업을 할 때 "매번 전부 다시 이해하는" 방식을 사용합니다:

- 문서 더미를 모델에 던진다
- 질문할 때 임시로 검색한다
- 일회성 답변을 생성한다
- 대화가 끝나면 구조는 남지 않는다

이 프로젝트는 다른 방향을 선택합니다:

- 원본 자료는 `raw/`에 들어온다
- LLM이 자료를 `wiki/`로 점진적으로 축적한다
- 새 자료가 들어오면 처음부터 다시 시작하는 게 아니라, 기존 지식 구조를 업데이트한다
- 높은 가치를 지닌 질문과 답변은 wiki에 역으로 기록될 수 있다

결과적으로: 지식은 매번 임시로 조합되는 것이 아니라, Obsidian wiki 안에 지속적으로 "컴파일"되어 축적됩니다.

## 원본 대비 이 프로젝트의 개선점

Karpathy 원문과 비교했을 때, 이 프로젝트는 `raw + wiki + schema` 3계층 구조를 유지하면서 다음을 추가했습니다:

- Obsidian 지향적 디렉토리 구조와 wiki 링크 스타일 (`[[이중 괄호 링크]]`)
- Codex용 [AGENTS.md](./inspool-wiki-zh/AGENTS.md)와 Claude용 [CLAUDE.md](./inspool-wiki-zh/CLAUDE.md)
- `.claude/`와 `.codex/` 디렉토리에 패키징된 재사용 가능한 `commands/`와 `skills/`
- 명확한 `unprocessed -> pending_review -> processed` 상태 머신
- 사용자 확인 후에만 raw 자료를 아카이브하는 검토 우선 흐름
- 서로 관련된 소규모 자료 묶음을 하나의 ingest 단위로 처리하는 기능
- `concepts / entities / synthesis` 아래 사용자가 직접 관리하는 서브폴더 호환
- 오래된 경로 참조, 약한 결론, 관계 필드 누락 등을 감지하는 `lint_wiki` 검사

## 빠른 시작

### 1. Obsidian Vault에 복사

`inspool-wiki-zh/`를 당신의 Obsidian Vault에 복사하세요. (영문 버전은 `inspool-wiki-en/`)

### 2. 에이전트 설정

에이전트가 규칙 파일을 읽도록 설정하세요:

- Codex: `inspool-wiki-zh/AGENTS.md`
- Claude: `inspool-wiki-zh/CLAUDE.md`

**영문 버전 사용 시**: `.claude-en/`을 `.claude/`로, `.codex-en/`을 `.codex/`로 이름을 변경하세요.

### 3. Obsidian Web Clipper 설치

[Obsidian Web Clipper](https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf)는 웹 페이지를 Markdown으로 변환하여 Obsidian Vault에 바로 저장하는 Chrome 확장 프로그램입니다.

- Chrome Web Store에서 "Obsidian Web Clipper"를 검색하거나 위 링크를 클릭하여 설치
- 설치 후 확장 설정에서 저장 폴더를 `inspool-wiki-zh/raw/unprocessed/`로 설정

### 4. 시작하기

1. Obsidian Web Clipper로 첫 번째 아티클을 수집
2. `ingest_raw` 실행
3. 결과를 검토한 후 `approve_ingest` 실행
4. `query_wiki`, `inspool`, `lint_wiki`로 wiki를 지속적으로 관리

## 표준 워크플로우

### 1. 수집 (Capture)

원본 자료를 `raw/unprocessed/`에 넣습니다.

자료는 다음과 같은 것들이 될 수 있습니다:

- 웹 클리핑
- 아티클 또는 도서 발췌
- 회의록
- PDF 변환본
- 장기적으로 축적할 가치가 있는 기타 Markdown 문서

### 2. Ingest

`ingest_raw`를 실행합니다.

이것이 수행하는 작업:

- 다음 ingest 단위 선택
- 소스 페이지 생성 또는 업데이트
- 관련 엔티티 페이지, 개념 페이지, 합성 페이지 업데이트
- `wiki/index.md` 업데이트
- `wiki/log.md`에 기록 추가
- `raw/index.md` 업데이트
- 현재 raw 배치를 `pending_review`로 표시

### 3. 검토 (Review)

이번 ingest 결과를 검토합니다.

주요 확인 사항:

- 페이지 이름이 적절한가
- 링크(이중 괄호 링크)가 유효한가
- 결론이 과장되지 않았는가
- 핵심 개념이나 엔티티를 빠뜨리지 않았는가
- 근거가 충분한가

### 4. Approve

ingest 결과가 올바르면 `approve_ingest`를 실행합니다.

이것이 수행하는 작업:

- 해당 raw 파일 상태를 `pending_review`에서 `processed`로 변경
- `raw/processed/`로 파일 이동
- wiki 페이지의 raw 경로 참조 복구
- `raw/index.md` 업데이트
- `wiki/log.md`에 기록 추가

핵심 제약:

**raw 파일은 사용자의 명시적 확인 후에만 이동됩니다.**

### 5. Query

일상적인 질문 시 `query_wiki`를 실행하여, raw 자료로 바로 돌아가는 대신 기존 wiki 페이지를 우선 참조하여 답변합니다.

### 6. Inspool

특히 가치 있는 답변이라면 `inspool`을 실행하여 wiki에 역으로 기록합니다. 보통 `wiki/synthesis/`에 새 페이지로 작성됩니다.

### 7. Lint

정기적으로 `lint_wiki`를 실행하여 다음을 확인합니다:

- 고립된 페이지 (연결 없는 페이지)
- 오래된 경로 참조
- 소스 근거 없는 강한 결론
- 관계 필드 누락
- 사용자 페이지 재분류 후 깨진 가정
- `raw/index.md`와 실제 디렉토리 상태 불일치

## 디렉토리 구조

```text
.claude/
├─ commands/              # Claude Code 명령어
└─ skills/                # Obsidian 공식 skill (공용)
.codex/
└─ skills/
   ├─ {core}/             # 핵심 skill (5개)
   └─ {obsidian}/         # Obsidian 공식 skill (공용, 5개)
inspool-wiki-zh/
├─ AGENTS.md
├─ CLAUDE.md
├─ raw/
│  ├─ unprocessed/
│  ├─ processed/
│  ├─ assets/
│  ├─ index.md
│  └─ README.md
└─ wiki/
   ├─ sources/
   ├─ entities/
   ├─ concepts/
   ├─ synthesis/
   ├─ meta/
   ├─ index.md
   └─ log.md
```

디렉토리 역할:

- `.claude/commands/`: Claude Code 명령어 템플릿
- `.codex/skills/`: Codex 핵심 skill + Obsidian 공식 skill
- `.claude/skills/`: Obsidian 공식 제공 공용 skill
- `raw/`: 원본 자료 계층. 본문은 기본적으로 읽기 전용이며, 최소한의 워크플로우 메타데이터만 수정 허용
- `wiki/`: 구조화된 지식 계층. 소스 페이지, 엔티티 페이지, 개념 페이지, 합성 페이지가 여기에 위치
- `AGENTS.md` / `CLAUDE.md`: 에이전트 규칙 진입점

## 수집 방법

권장 수집 방식은 **Obsidian Web Clipper**입니다 (빠른 시작 참조).

선택적 권장 사항:

- 아티클 이미지가 중요하다면 `raw/assets/`에 다운로드
- 원본 제목, URL, 날짜를 최대한 보존
- raw 노트의 본문은 수동으로 재작성하지 말 것

## 사용 원칙

장기적으로 유지 가능한 프로젝트를 위해 다음 원칙을 지키세요:

- `raw/`를 사실 소스 계층으로 취급하고 본문을 임의로 재작성하지 마세요
- `wiki/`는 시간이 지남에 따라 진화할 수 있는 합성 계층으로 취급하세요
- 핵심 주장은 외부 URL 대신 로컬 `sources` 페이지와 연결하세요
- 불확실한 내용은 "확인 필요" 또는 열린 질문으로 표시하세요
- 충돌하는 정보는 조용히 덮어쓰지 말고 명시적으로 기록하세요
- 사용자가 `concepts / entities / synthesis` 아래에 서브폴더를 만드는 것을 허용하세요
- 깊은 경로를 하드코딩하는 대신 Obsidian wiki 링크를 우선 사용하세요

## Ingest 단위

기본 처리 단위는 항상 "파일 하나"가 아닙니다. ingest 단위입니다.

ingest 단위는:

- 독립된 하나의 소스
- 명확하게 연관된 소규모 소스 묶음

그룹화 우선순위:

1. `raw/unprocessed/` 아래 같은 서브폴더의 파일들
2. 동일한 `ingest_group`을 가진 파일들
3. 사용자가 함께 처리하길 원한다고 명시한 파일들
4. 그 외에는 파일 하나씩 처리

권장 배치 크기: 2~5개 파일.

## 그래프 모델

이 프로젝트는 Obsidian 안에 탐색 가능한 지식 그래프를 구축하는 것을 목표로 합니다.

권장 계층:

- `raw/`: 원본 소스 노드
- `wiki/sources/`: 증거 노드
- `wiki/entities/`: 엔티티 노드
- `wiki/concepts/`: 개념 노드
- `wiki/synthesis/`: 합성 또는 판단 노드

핵심 원칙:

- 외부 URL은 주요 그래프 엣지가 아닙니다
- 중요한 관계는 로컬 Markdown 페이지를 연결해야 합니다
- `sources`는 증거 앵커 계층 역할을 합니다
- `supports / contradicts / related_*`는 frontmatter에서 구조화된 관계를 표현합니다
- 본문 내 wiki 링크는 읽기 흐름과 그래프 시각화를 지원합니다

## 명령어와 Skill

Codex skill:

- [ingest_raw](./.codex/skills/ingest_raw/SKILL.md)
- [approve_ingest](./.codex/skills/approve_ingest/SKILL.md)
- [query_wiki](./.codex/skills/query_wiki/SKILL.md)
- [inspool](./.codex/skills/inspool/SKILL.md)
- [lint_wiki](./.codex/skills/lint_wiki/SKILL.md)

Claude Code 명령어:

- [ingest_raw](./.claude/commands/ingest_raw.md)
- [approve_ingest](./.claude/commands/approve_ingest.md)
- [query_wiki](./.claude/commands/query_wiki.md)
- [inspool](./.claude/commands/inspool.md)
- [lint_wiki](./.claude/commands/lint_wiki.md)

공용 Obsidian skill (Obsidian 공식 제공):

- [defuddle](./.codex/skills/defuddle/SKILL.md)
- [json-canvas](./.codex/skills/json-canvas/SKILL.md)
- [obsidian-bases](./.codex/skills/obsidian-bases/SKILL.md)
- [obsidian-cli](./.codex/skills/obsidian-cli/SKILL.md)
- [obsidian-markdown](./.codex/skills/obsidian-markdown/SKILL.md)

## Codex와 Claude 사용 방식

- Codex 사용자는 [AGENTS.md](./inspool-wiki-zh/AGENTS.md)를 시작점으로, `.codex/skills/` 아래의 skill을 사용하세요.
- Claude 사용자는 [CLAUDE.md](./inspool-wiki-zh/CLAUDE.md)를 시작점으로, `.claude/commands/` 아래의 명령어를 사용하세요.

## 권장 Obsidian 플러그인

| 플러그인 | 역할 |
|---------|------|
| **Claudian** | Obsidian 내에서 Claude를 직접 호출. 이 프로젝트의 명령어를 사용하여 에디터 안에서 ingest, query 등의 워크플로우를 바로 실행 |
| **Templater** | 이 프로젝트의 규칙에 맞는 소스 페이지, 엔티티 페이지, 개념 페이지, 합성 페이지를 빠르게 생성하는 템플릿 엔진 |
| **Dataview** | frontmatter 필드 기반 동적 쿼리 뷰. `supports`, `contradicts` 등의 관계를 탐색하는 데 유용 |
| **Outliner** | 향상된 목록 및 개요 경험. wiki 페이지의 계층 구조와 접기 탐색에 적합 |
| **Another Quick Switcher** | 퍼지 검색과 wiki 링크 점프를 지원하는 향상된 빠른 전환기. wiki 페이지 간 탐색 속도 향상 |
| **File Explorer Note Count** | 파일 탐색기에서 폴더별 노트 수를 표시. `sources/`, `entities/` 등 디렉토리의 축적 현황을 직관적으로 파악 |
| **Custom Attachment Location** | 사용자 정의 첨부 파일 저장 경로. 이미지를 `raw/assets/`에 일관되게 저장하도록 설정 |

## 다음 확장 방향

- 더 세밀한 그래프 관계 제약 추가
- 도메인별 페이지 템플릿 추가
- Claude와 Codex 각각에 맞는 프롬프트 최적화

---

## QJC 적용 메모

본 fork는 QJC(퀀텀점프클럽) 1인기업의 LLM Wiki 시스템 베이스로 사용됩니다.

- 적용 범위: Strict 2 vault (대표 / 개인)
- 핵심 패턴: Karpathy `raw/ + wiki/ + schema/CLAUDE.md` 3중 구조 유지
- 변형 사항: Phase 0(grep) → Phase 1(임베딩) → Phase 2(자동화) 단계적 도입
- 적용 spec: `/Users/sangrok/docs/superpowers/specs/2026-05-20-obsidian-llm-wiki-design.md`
- fork 일자: 2026-05-20
