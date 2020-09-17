# Git-Convention-Guide

작성자: @paul kim 

---

 본 문서는 "WILT" 서비스를 개발위해 사용 사용될 Git사용을 위한 가이드라인을 제공합니다. 

---

---

# 1. Branching strategy

---

## 1. Main Branches

### 1. `master`

- `origin/master`는 항상 최신 소스를 반영하는 branch
- 개발자는 항상 `master` branch를 branching 하거나 merge.

### 2. `stable`

- `origin/stable`은 production 에 배포된 최신의 코드를 반영.
- 이는, 개발이 진행되는 동안 stable 상태의 코드가 변경되는 것을 방지.
- `master` branch가 stable 상태이고, 배포 완료시 해당 branch는 `stable` branch에 merge & version tagging.

## 2. Supporting Branches

### 1. `feature`

- `origin/feature-<tbd number>` 는 새로운 feature를 개발 할 때 사용.
- Must branch from: `master`
- Must merge back into: `master`
- Branch naming convention: `feature-<tbd number>`
- `<tbd number>` represents the Basecamp project to which Project Management will be tracked.

```
# Make new feature branch
$ git checkout -b feature-id master                 // creates a local branch for the new feature
$ git push origin feature-id                        // makes the new feature remotely available

# Periodically, merge into your feature branch
$ git merge master                                  // merges changes from master into feature branch

# When development on the feature is complete
$ git checkout master                               // change to the master branch  
$ git merge --no-ff feature-id                      // makes sure to create a commit object during merge
$ git push origin master                            // push merge changes
$ git push origin :feature-id                       // deletes the remote branch
```

### 2. `bug`

- `origin/bug-<tbd number>` 는 수정되어야 할 버그 발견시, 버그 수정을 위해 사용.
- `bug` branch는 일반적으로 배포 주기 내에 포함되어 `master`에 merge 되어야 한다..
- Must branch from: `master`
- Must merge back into: `master`
- Branch naming convention: `bug-<tbd number>`

```
# Make new bug branch
$ git checkout -b bug-id master                     // creates a local branch for the new bug
$ git push origin bug-id                            // makes the new bug remotely available

# Periodically, merge into your feature branch
$ git merge master                                  // merges changes from master into feature branch

# When development on the bug is complete
$ git checkout master                               // change to the master branch  
$ git merge --no-ff bug-id                          // makes sure to create a commit object during merge
$ git push origin master                            // push merge changes
$ git push origin :bug-id                           // deletes the remote branch
```

### 3. `hotfix`

- `origin/hotfix-<tbd number>` 는 production version 바로 반영 시켜야할 치명적인 버그를 수정하기 위해 사용.
- 긴급한 사안이기 때문에, 배포 일정과 관계 없이 바로 push 한다.

- Must branch from: tagged `stable`
- Must merge back into: `master` and `stable`
- Branch naming convention: `hotfix-<tbd number>`

```
# Make new hotfix branch
$ git checkout -b hotfix-id stable                  // creates a local branch for the new hotfix
$ git push origin hotfix-id                         // makes the new hotfix remotely available

# When development on the hotfix is complete
$ git checkout stable                               // change to the stable branch
$ git merge --no-ff hotfix-id                       // forces creation of commit object during merge
$ git tag -a <tag>                                  // tags the fix
$ git push origin stable --tags                     // push tag changes

# Merge changes into master so not to lose the hotfix and then delete the remote hotfix branch.
$ git checkout master                               // change to the master branch
$ git merge --no-ff hotfix-id                       // forces creation of commit object during merge
$ git push origin master                            // push merge changes
$ git push origin :hotfix-id                        // deletes the remote branch
```

### 3. Workflow Diagram

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c42699cf-fe19-4448-bee7-f5ca0bcd1af9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c42699cf-fe19-4448-bee7-f5ca0bcd1af9/Untitled.png)

# 2. Commit message

---

### Commit message template

- Commit message는 기본적으로 아래의 Template를 따라 작성한다.
- 제목(`<type>:<title>`)은 *반드시* 입력하고, 최대 40글자 까지 영문 명령조로 작성
- 본문(`<description>`)은 어떻게 보다는 무엇을, 왜 변경 했는지를 작성(선택)
- 이슈넘버(`<issue-number>`)는 연관된 이슈를 기재하며, 여러 개 추가 가능하다.(선택)

```
Feat: Implement user model class 

<description>
로그인 기능 구현을 위해, User 모델 작성. 

<issue-number>
#512
```

### How to apply template file

- Commit message template file과 해당 템플릿 파일을 적용하는 방법
1. `.git-commit-template.txt` 작성

/your/project/root/path/.**git-commit-template.txt**

```
<type>: <title> 
# <type
# - feat   : 새로운 기능 추가
# - fix    : 버그 수정 
# - docs   : 문서 수정
# - style  : 코드 포맷팅, 세미콜론 누락, 코드 변경이 없는 경우
# - refact : 코드 리펙토링
# - test   : 테스트 코드, 리펙토링 테스트 코드 추가
# - chore  : 빌드 업무 수정, 패키지 매니저 수정

# <title>
# 제목의 길이는 최대 40글자까지 한글로 간단 명료하게 작성 
# <type>: 한칸 띄고 <title 작성>
# 제목을 작성하고 반드시 빈 줄 한 줄을 만들어야 함 
# 제목에 .(마침표) 금지 

<description> 
# 내용의 길이는 한 줄당 60글자 내외에서 줄 바꿈. 한글로 간단 명료하게 작성 
# 어떻게 보다는 무엇을, 왜 변경했는지를 작성할 것 (필수) 

<issue-number> 
# 연관된 이슈 첨부, 여러 개 추가 가능2. 프로젝트에 commit template 적용
```

2. `commit.template` 등록

```bash
# On your project root
$ git config --global commit.template ./.git-commit-template.txt
```

# 3. Code formatting with `black` for `Python`

- "`black`", "`pre-commit`"을 사용
- Commit전, Python PEP-8 code convention을 체크해 보자

### Install `black`, `pre-commit`

```
pip install black pre-commit
```

### Write and install `.pre-commit-config.yaml`

1. `.pre-commit-config.yaml` 프로젝트 경로 최상단에 다음과 같이 작성한다.

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
  - repo: https://github.com/psf/black
    rev: stable
    hooks:
      - id: black
```

2. `pre-commit` install 

```bash
$ pre-commit install
pre-commit installed at .git/hooks/pre-commit
```

3. 최초 설정시, 모든 파일 사전 검사

```bash
$ pre-commit run --all-files
[INFO] Initializing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Initializing environment for https://github.com/psf/black.
[INFO] Installing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
[INFO] Installing environment for https://github.com/psf/black.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
Check Yaml...............................................................Passed
Fix End of Files.........................................................Passed
Trim Trailing Whitespace.................................................Failed
- hook id: trailing-whitespace
- exit code: 1

Files were modified by this hook. Additional output:

Fixing sample.py

black....................................................................Passed
```

- 앞으로 Commit을 진행하면 항상 `black`이 code convention을 검사하고, 실패시 자동으로 바꿔준다.

```bash
git commit -m "Apply balck linter"
Check Yaml...............................................................Passed
Fix End of Files.........................................................Passed
Trim Trailing Whitespace.................................................Passed
black....................................................................Passed
[master 3a45278] Apply balck linter
 36 files changed, 333 insertions(+), 294 deletions(-)
 delete mode 100644 .github/workflows/black.yml
 rewrite fc_django/fcuser/migrations/0001_initial.py (67%)
 rewrite fc_django/order/migrations/0001_initial.py (69%)
 rewrite fc_django/order/models.py (61%)
 rewrite fc_django/product/migrations/0001_initial.py (71%)
 rewrite fc_django/product/models.py (64%)
```

# Reference

[Git/GitHub branching standards & conventions](https://gist.github.com/digitaljhelms/4287848)

---

[지금 당장 좋은 커밋 메시지를 남기는 방법(with Git Commit Template)](https://jeong-pro.tistory.com/207)

[Udacity Nanodegree Style Guide](https://udacity.github.io/git-styleguide/)

---

[pre-commit](https://pre-commit.com/)

[psf/black](https://github.com/psf/black)
