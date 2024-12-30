---
title: "github webhook issue_comment와 pull_request_comment는 무엇일까요?"
date: 2024-12-30 10:00:00 +/- TTTT
categories: [Dev, GitHub]
tags: [NodeJS]
pin: true
---

# github PullRequest comment 작성 시, slack 알림 설정

슬랙 알림을 받으려면 gitHub Setting에서 WebHook을 추가해야 하는데,
`Which events would you like to trigger this webhook?`에 `Let me select individual events.`을 클릭하면 어떤 이벤트에 대한 알림을 받을 건지 웹훅을 커스텀 할 수 있다.  
처음에는 `pull_request_review_comment`만 체크했었는데, 이렇게 하니 FileChanged에서 추가한 single comment에 대한 알림만 오고 PullRequest 하단에 comment에 대한 알림이 오지 않았다.

webhook 요청 로그를 보니 Pull Request 하단에서 comment를 작성할 시 `webhook Internal Server Error` 발생하고 있었다.

### 원인

pull_request_comment 만 추가해놨기 때문이다.

**comment는 두 가지로 구분된다.**

1. issue_comment
2. pull_request_review_comment

Pull Request 하단에서 **"Add a comment"**를 통해 작성된 코멘트는 issue 필드에 포함되어 온다.
이는 Pull Request 자체가 GitHub의 구조상 이슈의 일종으로 취급되기 때문이다.

Pull Request의 "Files changed" 탭에서 작성된 코멘트는 pull_request 필드에 포함되어 온다.
이는 파일 변경 사항과 직접적으로 연결되며, pull_request_review_comment 이벤트로 트리거된다.

**요약**

- 하단 일반 코멘트 → issue 필드에 데이터 포함.
- 파일 변경사항 코멘트 → pull_request 필드에 데이터 포함.

### 해결방안

`Let me select individual events.`에 `issue_comment`도 체크하고,  
issue와 pull_request를 둘다 수용할 수 있도록 CommentEventResponseDto를 수정하였다.
