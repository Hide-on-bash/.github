# 브랜치 전략 및 PR 가이드


## 브랜치 전략: Git Flow

우리 팀은 **Git Flow**를 사용합니다.

```
feat/기능A ──┐
feat/기능B ──┼──→ develop ──→ (검증 완료) ──→ main
feat/기능C ──┘

                         ↑
                  hotfix/긴급수정 ──→ main (긴급 시)
```

### 왜 GitHub Flow가 아닌 Git Flow인가?

| | GitHub Flow | Git Flow |
|---|---|---|
| 구조 | 피처 브랜치 → main 직접 | 피처 브랜치 → develop → main |
| 적합한 상황 | 혼자 or 소규모, 빠른 배포 | **팀 협업, 버전 관리 필요** |
| main 안정성 | 항상 유지하기 어려움 | **항상 배포 가능한 상태 유지** |

---

## 브랜치 종류와 역할

### 1. `main(master)`

- **실제로 배포된 코드**가 있는 브랜치
- 항상 완전히 동작하는, **배포 가능한 상태**여야 함
- `develop`이 충분히 검증된 후에만 merge
- **직접 커밋 절대 금지**

### 2. `develop`

- **다음 버전을 준비하는 통합 브랜치**
- 팀원들의 피처 브랜치가 여기로 merge됨
- 다소 불안정한 상태가 있을 수 있음
- **직접 커밋 금지** — 반드시 PR로 merge

### 3. `feat/<기능명>`

- **새 기능을 개발하는 브랜치** (가장 자주 만드는 브랜치)
- `develop`에서 분기 → 완료되면 `develop`으로 PR
- 기능 1개 = 브랜치 1개
- merge 완료 후 삭제

### 4. `fix/<수정내용>`

- **버그 수정 브랜치**
- `develop`에서 분기 → 완료되면 `develop`으로 PR

### 5. `refactor/<내용>`

- **기능 변화 없이 코드 구조를 개선하는 브랜치**
- `develop`에서 분기 → 완료되면 `develop`으로 PR

### 6. `hotfix/<내용>`

- **배포 중인 main에서 긴급 버그 발생 시에만** 사용
- `main`에서 분기 → `main`과 `develop` 양쪽에 merge
- 일반적인 상황에서는 사용하지 않음

### 7. `docs/<내용>` (선택)

- README, 명세서 등 문서만 수정할 때 사용

---

## 브랜치 네이밍 규칙

```
feat/data-loader          ✅ 소문자, 단어 사이 하이픈(-)
fix/null-check            ✅
feat/api-integration      ✅

feat/DataLoader           ❌ 카멜케이스 사용 금지
feat/데이터로더            ❌ 한국어 금지
feat/test                 ❌ 너무 추상적
```

| 규칙 | 예시 |
|------|------|
| `타입/설명` 형식 | `feat/report-export` |
| 소문자만 사용 | `fix/input-validation` |
| 단어 구분은 하이픈(`-`) | `feat/api-integration` |
| 구체적인 이름 | `feat/parse-response` (❌ `feat/fix2`) |

---

## 작업 흐름 (step-by-step)

### 새 기능을 개발할 때

```bash
# 1. develop 최신 상태로 업데이트
git checkout develop
git pull origin develop

# 2. 피처 브랜치 생성
git checkout -b feat/data-loader

# 3. 작업 후 커밋 (commit 컨벤션 참고)
git add .
git commit -m "feat: CSV 데이터 로드 및 전처리 구현"

# 4. 원격에 push
git push origin feat/data-loader

# 5. GitHub에서 PR 생성 → 리뷰 → merge (아래 PR 섹션 참고) -> merge 후 브랜치 삭제

# 6. merge 완료 후 로컬 브랜치 삭제
git checkout develop
git pull origin develop
git branch -d feat/data-loader
```

### 작업 도중 develop에 다른 팀원 변경사항이 생겼을 때

내 피처 브랜치에 develop 최신 내용을 반영하는 방법입니다.

```bash
# 원격의 최신 상태 가져오기
git fetch origin

# 내 피처 브랜치에서 develop 최신 내용 merge
git checkout feat/data-loader
git merge origin/develop
```

이 과정에서 merge commit이 하나 생깁니다. 충돌(conflict)이 있으면 수동으로 해결 후 커밋합니다.

```bash
# 충돌 해결 후
git add .
git commit -m "chore: develop 최신 변경사항 반영"
git push origin feat/data-loader
```

---

## PR (Pull Request) 가이드

### PR이란?

"내 브랜치의 변경사항을 develop에 합쳐달라"는 요청.
단순히 코드를 합치는 게 아니라 **팀원이 코드를 검토(리뷰)하는 자리**입니다.

---

### PR 제목 규칙

커밋 컨벤션과 동일하게 `타입: 내용` 형식으로 작성합니다.

```
feat: 데이터 시각화 모듈 구현
fix: API 응답 None 처리 누락 수정
refactor: JSON 파싱 함수 utils.py로 중앙화
docs: README API 명세 섹션 추가
```

---

### PR 본문 템플릿

```markdown
## 작업 내용

- [ ] [구현 항목 1]
- [ ] [구현 항목 2]
- [ ] [구현 항목 3]

## 변경 이유

[기존 방식의 문제점, 또는 새 기능이 필요한 이유]

## 테스트 방법

1. [실행 방법]
2. [확인 방법]
3. [예상 결과]
```

---

### PR 리뷰 방법

#### 리뷰어 — 리뷰 요청받은 사람

**리뷰어로 지정되면 24시간 이내에 완료해주세요.**

**1. PR 페이지 → "Files changed" 탭 클릭**

변경된 파일 전체를 여기서 확인합니다.

**2. 코드 줄에 댓글 달기**

코드 줄에 마우스를 올리면 파란 `+` 버튼이 나타납니다 → 클릭 → 댓글 작성 → **"Start a review"** 클릭

> "Add single comment"는 즉시 올라가고, "Start a review"는 모아서 한번에 제출됩니다. **Start a review 권장**

댓글 앞에 아래 태그를 붙여 의도를 명확히 합니다:

| 표현 | 의미 |
|------|------|
| `[필수]` | 반드시 수정해야 merge 가능 |
| `[제안]` | 수정하면 더 좋지만 선택사항 |
| `[질문]` | 이해가 안 돼서 물어보는 것 |

```
[필수] 여기서 None 체크를 하지 않으면 런타임 에러가 날 수 있어요.
[제안] 이 분기 로직 별도 함수로 추출하면 테스트하기 편할 것 같아요.
[질문] 이 값을 리스트로 관리하는 이유가 있나요?
```

**3. 리뷰 완료 → "Review changes" 버튼 클릭 → 결정 선택 → "Submit review"**

| 결정 | 의미 |
|------|------|
| **Approve** | "괜찮아요, merge해도 됩니다" |
| **Request changes** | "이 부분 고쳐주세요, 수정 후 다시 볼게요" |
| **Comment** | "그냥 의견만 남기는 거예요 (승인/거절 아님)" |

---

#### PR 작성자 — 리뷰 받은 사람

**1. PR 페이지 또는 알림에서 댓글 확인**

**2. 수정이 필요하면 로컬에서 수정 후 같은 브랜치에 push**

```bash
git add .
git commit -m "fix: None 체크 로직 추가"
git push origin feat/data-loader
```

같은 브랜치에 push하면 PR이 자동으로 업데이트됩니다. 새 PR을 만들 필요 없습니다.

**3. 수정 완료 후 댓글에 답글 남기고 재리뷰 요청**

PR 페이지 오른쪽 Reviewers 옆 🔄 버튼 클릭 → 리뷰어에게 재요청

**4. Approve 받으면 본인이 "Create a merge commit" 클릭**

---

### PR merge 방법

**Approve가 1개 이상 받은 후 merge합니다.**

우리 팀은 **Create a merge commit**을 사용합니다.

GitHub PR 화면에서 merge 버튼을 클릭하면 아래와 같은 merge commit이 develop에 생깁니다:

```
Merge feat/data-loader into develop
```

이 방식은 "피처 브랜치가 어느 시점에 develop에 들어왔는지" 기록이 남아서 히스토리 추적이 쉽습니다.

> merge 버튼은 **PR 작성자가 Approve 받은 후 직접 누릅니다.**

---

### merge 후 해야 할 것

**merge한 사람이 GitHub에서 브랜치를 삭제합니다.** (GitHub PR 화면 하단 "Delete branch" 버튼)

```bash
# 로컬에서도 삭제
git checkout develop
git pull origin develop
git branch -d feat/data-loader
```

---

### 원격에서 삭제된 브랜치가 git graph에 계속 보일 때

GitHub에서 브랜치를 삭제해도 로컬 git graph에는 계속 표시됩니다.
`--prune` 옵션으로 원격에서 없어진 브랜치 참조를 정리합니다.

```bash
git fetch --prune
```

매번 자동으로 정리하고 싶다면 한 번만 설정:

```bash
git config --global fetch.prune true
```

설정 후에는 `git fetch`, `git pull` 때 자동으로 정리됩니다.

---

## 브랜치 보호 설정 (관리자가 설정)

GitHub 리포지토리 → Settings → Branches → Branch protection rules

`main`과 `develop` 브랜치에 아래 설정을 해두세요:

```
✅ Require a pull request before merging
✅ Require approvals (최소 1명)
✅ Dismiss stale pull request approvals when new commits are pushed
✅ Do not allow bypassing the above settings
```

이 설정을 하면 PR 없이는 물리적으로 push가 불가능해집니다.

---

## 절대 하면 안 되는 것 🚫

### 1. main, develop에 직접 커밋 (push) 금지

```bash
git checkout develop
git push origin develop    # ❌ 절대 금지
```

반드시 피처 브랜치 → PR → 리뷰 → merge 순서를 지키세요.

---

### 2. main, develop에 force push 금지

```bash
git push --force origin main     # ❌ 절대 금지
git push --force origin develop  # ❌ 절대 금지
```

다른 팀원이 이미 pull해간 커밋 히스토리가 망가집니다. 최악의 경우 작업한 코드가 사라질 수 있습니다.

---

### 3. 리뷰 없이 혼자 merge 금지

본인이 PR 열고, 본인이 Approve하고, 본인이 merge → ❌

---

### 4. feat → main으로 직접 PR 금지

```
feat/data-loader → main    ❌ (잘못된 대상)
feat/data-loader → develop ✅ (올바른 대상)
```

---

### 5. 오래된 브랜치 방치 금지

merge 완료된 브랜치는 즉시 삭제하세요.

---

### 6. PR 없이 구두로 "됐어요" 금지

모든 리뷰와 승인은 **GitHub PR 화면에서** 진행합니다.

---

## 요약 치트시트

```
새 기능 개발:
  develop → feat/기능명 분기 → 작업 → PR → develop merge

버그 수정:
  develop → fix/버그내용 분기 → 수정 → PR → develop merge

긴급 배포 버그:
  main → hotfix/내용 분기 → 수정 → main + develop 양쪽 merge

배포:
  develop (검증 완료) → PR → main merge
```

```
브랜치 네이밍:
  feat/data-loader          ✅
  fix/input-validation      ✅
  feat/DataLoader           ❌ (카멜케이스)
  feat/수정                 ❌ (한국어)
```

```
절대 금지:
  ❌ main, develop에 직접 push
  ❌ 공유 브랜치에 force push
  ❌ 리뷰 없이 본인이 merge
  ❌ feat → main으로 직접 PR
  ❌ 오래된 브랜치 방치
```

---

*작성: 박태정 | 브랜치 전략: Git Flow | merge 방식: Create a merge commit*
