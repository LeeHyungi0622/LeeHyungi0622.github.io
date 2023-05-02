---
title: 210130 Git Commit Message TIL
date: 2021-01-30 11:18:42
tags:
  - Git
  - TIL
  - Self-Development
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210130_commit_message.png" alt="Git commit message"/>
</div>

<br/>

- ## **Commit Convention**

  - commit제목은 50자 이내로 요약하여 작성한다.
  - 제목과 내용사이에는 한 칸 띄어준다.
  - prefix를 활용하여 commit의 용도를 한 눈에 알아볼 수 있도록 한다.

  ```
  feat: features
  docs: documentations
  conf: configurations
  test: test
  fix: bug-fix
  refactor: refactoring
  ci: Continuous Integration
  build: Build
  perf: Performance
  ```

  <!-- more -->

  **ex) Commit Convention - example**

  ```
  feat: Create server.py to start flask project
  docs: Create README.md
  conf: poetry init
  test: User model CRUD test complete
  ```

  구글링하다가 commit convention에 대해서 정리가 잘 되어있는 페이지가 있어서 정리해보려고 한다.

<br/>

- ## Semantic Commit Messages

  See how a minor change to your commit message style can make you a better programmer.

  Format: `<type>(<scope>): <subject>`

  `<scope>` is optional

  ## Example

  ```
  feat: add hat wobble
  ^--^  ^------------^
  |     |
  |     +-> Summary in present tense.
  |
  +-------> Type: chore, docs, feat, fix, refactor, style, or test.
  ```

  More Examples:

  - `feat`: (new feature for the user, not a new feature for build script)
  - `fix`: (bug fix for the user, not a fix to a build script)
  - `docs`: (changes to the documentation)
  - `style`: (formatting, missing semi colons, etc; no production code change)
  - `refactor`: (refactoring production code, eg. renaming a variable)
  - `test`: (adding missing tests, refactoring tests; no production code change)
  - `chore`: (updating grunt tasks etc; no production code change)

  References:

  - https://www.conventionalcommits.org/
  - https://seesparkbox.com/foundry/semantic_commit_messages
  - http://karma-runner.github.io/1.0/dev/git-commit-msg.html

위의 참고자료들을 보고, 프로젝트시에 적극 활용해서 commit convention을 따라 standard한 commit message를 잘 적을 수 있는 개발자가 될 수 있도록 하자.

`목표 추가 : 협업 잘하는 개발자가 되기`
