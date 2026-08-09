# agent-skills

Agent Studio가 동기화하는 스킬 소스 저장소다. 이 저장소가 synced 스킬의
single source of truth이고, Agent Studio의 `/skills` → **Sync from GitHub**
버튼(또는 `POST /api/skills/sync`)이 여기 내용을 가져간다.

## 구조

```
skills/
  <skill-name>/        # 소문자-하이픈 슬러그 (스킬 이름)
    SKILL.md           # frontmatter + 마크다운 본문
    references/        # 선택. 필요할 때만 로드되는 참고 파일
      *.md
```

스킬 이름은 **디렉터리 이름**이다. sync는 `<디렉터리>/SKILL.md`를 찾아 부모
디렉터리명을 슬러그로 쓰고, `skills/` 아래 두는 건 관례일 뿐 강제는 아니다.
슬러그는 소문자·숫자·하이픈만 허용된다 (`^[a-z0-9-]+$`).

## SKILL.md 형식

```markdown
---
name: my-skill
description: 한 줄 설명 (스킬 목록과 모델의 시스템 프롬프트에 노출)
---

# 스킬 본문

모델이 Skill 툴로 로드했을 때 따라야 할 지침을 마크다운으로 작성한다.
```

- frontmatter는 완전한 YAML이 아니다. 평평한 `key: value` 줄과 `>`·`|` 폴딩
  스칼라만 읽고 나머지는 **조용히 무시**한다. 중첩 키나 리스트는 사라진다.
- `name`은 파싱하지 않는다 — 이름은 디렉터리가 정한다. 사람이 읽기 좋으라고
  남겨두는 건 괜찮지만, 디렉터리명과 다르면 디렉터리가 이긴다.
- `description`을 생략하면 본문의 첫 제목(없으면 첫 줄)을 200자까지 잘라 쓴다.

## description이 라우팅을 결정한다

시스템 프롬프트에는 `## Available Skills` 표에 **이름과 description만** 들어가고,
본문은 모델이 `Skill` 툴을 호출할 때 로드된다 (progressive disclosure).

엔진은 "필요한 작업을 **어떻게** 수행할지 지침이 필요하면 스킬을 로드하라"고
안내한다. 그러니 description은 *무엇인지*가 아니라 **언제 로드할지**를 말해야
한다. 자세한 작성법은 [`skills/skill-writer`](skills/skill-writer/SKILL.md)에
있다.

`skill_name`은 연결된 스킬 목록으로 enum이 걸려 있어서, 모델이 없는 스킬 이름을
지어내지는 못한다.

## references/ — 두 번째 단계 로딩

스킬 디렉터리 안의 파일은 sync가 함께 가져가고, 모델이 `Skill` 툴의 `file_path`로
필요할 때만 읽는다. 본문에 다 넣기엔 큰 자료(대량 매핑 표, 스타일 가이드 전문)를
분리할 때 쓴다.

| 항목 | 제한 |
|---|---|
| 확장자 | `.md` `.txt` `.json` `.yaml` `.yml` `.csv` |
| 파일당 | 64KB |
| 스킬당 | 20개 / 합계 200KB |

- 실행 스크립트와 바이너리는 **의도적으로 제외**된다. `Skill` 툴은 읽을 수 있는
  참고 자료만 돌려준다.
- 심볼릭 링크, 절대 경로, `..`는 거부된다.
- 캡에 걸린 파일은 sync 리포트에 `attachment` skip으로 파일명·사유와 함께 보고된다.
  스킬 자체는 등록되므로, 문서가 말하는 것보다 적게 실린 채 조용히 넘어갈 수 있다.
- 본문 몇십 줄을 쪼개는 용도로는 쓰지 마라. 파일 왕복이 한 번 더 생겨서 손해다.

## 반영 방법

main에 머지하고 Sync 버튼을 누르면 **아직 등록되지 않은 이름만** 새로 등록된다.

**이미 등록된 이름은 건드리지 않는다.** 콘솔에서 누군가 고쳤을 수 있고, sync는
그것과 "문서가 그냥 앞서간 것"을 구분할 방법이 없기 때문이다. 대신 문서가 바꿀
필드를 `existing`으로 보고하고, 콘솔에서 그 이름을 지목해야(`overwrite`) 실제로
덮어쓴다. 덮어쓸 때 움직이는 건 `description`·`content`·`files` 셋뿐이다.

**디렉터리를 지워도 스킬은 사라지지 않는다.** 이전 sync가 만든 스킬이 저장소에서
없어지면 `orphaned`로 **보고만** 되고, 콘솔에서 지목해야(`remove`) 삭제된다.
콘솔에서 직접 만든 스킬은 애초에 이 저장소 것이 아니라 목록에 뜨지도 않는다.

`POST /api/skills/sync`는 admin 권한이 필요하고 `{ overwrite?: [...], remove?: [...] }`를
받는다. 리포트는 `created` / `existing` / `overwritten` / `orphaned` / `removed` /
`skipped`로 돌아온다.
