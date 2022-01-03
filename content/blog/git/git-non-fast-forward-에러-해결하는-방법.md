---
title: Git non-fast-forward 에러 해결하는 방법
date: 2021-07-25 02:07:59
category: git
draft: false
---

## 상황

1. 원격 저장소에 REAME 파일을 생성
2. 로컬에서 해당 원격 저장소를 add 한 뒤(git remote add origin 'url'), 작업 진행
3. 로컬에서 작업 완료
4. 원격 저장소에 push 할 때 아래와 같은 에러 발생

```
git push origin master

To github url
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'github url'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

## 원인
원격 저장소에서 수행 작업과 로컬에서 수행한 작업간의 공통 분모가 없는 상태이기 때문에 발생

## 해결 방법

### 1. 관련 없었던 두 저장소를 병합

```
git pull origin master --allow-unrelated-histories 
```

### 2. 로컬에서 수행한 작업을 강제로 덮어 쓰기

```
git push origin +master
```