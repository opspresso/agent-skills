# agent-skills

Agent Studio가 동기화하는 스킬 소스 저장소예요. 이 저장소가 synced 스킬의
single source of truth이고, Agent Studio의 `/skills` → **Sync from GitHub**
버튼(또는 `POST /api/skills/sync`)이 여기 내용을 가져가요.

## 구조

```
skills/
  <skill-name>/        # 소문자-하이픈 슬러그 (스킬 이름)
    SKILL.md           # frontmatter + 마크다운 본문
```

## SKILL.md 형식

```markdown
---
name: my-skill
description: 한 줄 설명 (스킬 목록과 모델의 시스템 프롬프트에 노출)
---

# 스킬 본문

모델이 Skill 툴로 로드했을 때 따라야 할 지침을 마크다운으로 작성해요.
```

- `description`은 한 줄을 권장하지만 `>` 폴딩 멀티라인도 지원해요.
- 본문은 progressive disclosure 방식이라, 시스템 프롬프트에는 이름+설명만
  들어가고 전체 내용은 모델이 필요할 때 로드해요.

## 반영 방법

main에 머지하면 Agent Studio에서 Sync 버튼 한 번으로 반영돼요. 이름이 같은
스킬은 저장소 내용으로 덮어써지고, Agent Studio에서 직접 만든 다른 이름의
스킬은 건드리지 않아요.
