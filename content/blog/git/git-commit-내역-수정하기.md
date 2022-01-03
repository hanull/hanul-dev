---
title: git commit 내역 수정하기
date: 2021-08-07 02:08:52
category: git
draft: false
---

## 커밋 메시지 수정하기

### 1. 가장 최근의 커밋 수정

```
git commit --amend
```

`amend` 옵션을 사용하면 가장 마지막에 commit 한 내용을 보여준다. 수정 완료한 후 저장하고 나오면 된다. `esc -> :wq`



### 2. 더 오래전의 커밋 수정 && 한 번에 여러 커밋 수정

먼저 어떤 커밋을 수정할 것인지 확인해야 한다. 따라서  `git log` 명령어를 통해서 커밋 내역을 확인한다. 

만약 위에서 5번째 커밋 중 수정하고 싶은 커밋이 존재한다면, `git rebase -i HEAD~5`  명령어를 입력하면 된다. 해당 명령어를 입력하면 가장 최근 5개의 커밋을 보여준다. 

```
pick e499d89 commit message1
pick 0c39034 commit message2
pick f7fde4a commit message3
pick ggfde4b commit message4
pick 0cfde4c commit message5


# Rebase 9fdb3bd..f7fde4a onto 9fdb3bd
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

이제 수정하고 싶은 커밋 옆의 `pick` 을 `reword` 로 바꿔주면 된다. 이후 커밋 메시지를 수정하고 저장하면 된다.

```
pick e499d89 commit message1
pick 0c39034 commit message2
reword f7fde4a commit message3
reword ggfde4b commit message4
pick 0cfde4c commit message5
```

