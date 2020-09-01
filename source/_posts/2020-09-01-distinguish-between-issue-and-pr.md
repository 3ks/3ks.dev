---
title: 如何在 Webhook 数据中区分 issue 和 pr
thumbnail: 'https://cdn.sguan.top/markdown/20200901095255.png'
disqusId: 36e4e8e9-ed30-4502-8d31-c2a17e07809c
categories:
  - ["Github","Webhook"]
tags:
  - Github
  - Webhook
  - Issue
  - Pull Request
date: 2020-09-01 09:44:38
---

GitHub 的 issue 和 pull request 是两个不同的对象，但实际上，在一个仓库中，这两个对象的 ID 是不重复的，也就是说 issue 和 request 共享一类 ID。


<!-- more -->

## 共享一类 ID

在访问 GitHub 的时候，issue 的地址一般类似于这样：

```url
https://github.com/1kib/new/issue/1380
```

pull request 的地址一般是这样：

```url
https://github.com/1kib/new/pull/1381
```

我们其实可以将上面两个连接中的 `issue` 和 `pull` 字符互换，而不影响页面的访问，GitHub 会自动重定向，因为 ID 是不重复的。

## Webhook

由于它们的 ID 未作区分，创建 Webhook 后，在处理 comment payload 时，可能会导致错误，因为 issue comment 和 pr comment 公用的同一个 payload，数据内容相似度非常高。

例如：

{% codeblock "issue_comment_payload.json" lang:json  >folded %}
{
    "action":"created",
    "issue":{
        "url":"https://api.github.com/repos/1kib/new/issues/1380",
        "labels_url":"https://api.github.com/repos/1kib/new/issues/1380/labels{/name}",
        "comments_url":"https://api.github.com/repos/1kib/new/issues/1380/comments",
        "events_url":"https://api.github.com/repos/1kib/new/issues/1380/events",
        "html_url":"https://github.com/1kib/new/issues/1380",
        "id":689722355,
        "node_id":"MDU6SXNzdWU2ODk3MjIzNTU=",
        "number":1380,
        "title":"Test Issue",
        "user":{
            "login":"3ks",
            "id":32058002,
            "node_id":"MDQ6VXNlcjMyMDU4MDAy",
            "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/3ks",
            "html_url":"https://github.com/3ks",
            "followers_url":"https://api.github.com/users/3ks/followers",
            "following_url":"https://api.github.com/users/3ks/following{/other_user}",
            "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
            "organizations_url":"https://api.github.com/users/3ks/orgs",
            "repos_url":"https://api.github.com/users/3ks/repos",
            "events_url":"https://api.github.com/users/3ks/events{/privacy}",
            "received_events_url":"https://api.github.com/users/3ks/received_events",
            "type":"User",
            "site_admin":false
        },
        "labels":[
        ],
        "state":"open",
        "locked":false,
        "assignee":null,
        "assignees":[
        ],
        "milestone":null,
        "comments":2,
        "created_at":"2020-09-01T00:58:56Z",
        "updated_at":"2020-09-01T01:13:01Z",
        "closed_at":null,
        "body":"123"
    },
    "comment":{
        "url":"https://api.github.com/repos/1kib/new/issues/comments/684132075",
        "html_url":"https://github.com/1kib/new/issues/1380#issuecomment-684132075",
        "issue_url":"https://api.github.com/repos/1kib/new/issues/1380",
        "id":684132075,
        "node_id":"MDEyOklzc3VlQ29tbWVudDY4NDEzMjA3NQ==",
        "user":{
            "login":"3ks",
            "id":32058002,
            "node_id":"MDQ6VXNlcjMyMDU4MDAy",
            "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/3ks",
            "html_url":"https://github.com/3ks",
            "followers_url":"https://api.github.com/users/3ks/followers",
            "following_url":"https://api.github.com/users/3ks/following{/other_user}",
            "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
            "organizations_url":"https://api.github.com/users/3ks/orgs",
            "repos_url":"https://api.github.com/users/3ks/repos",
            "events_url":"https://api.github.com/users/3ks/events{/privacy}",
            "received_events_url":"https://api.github.com/users/3ks/received_events",
            "type":"User",
            "site_admin":false
        },
        "created_at":"2020-09-01T01:13:01Z",
        "updated_at":"2020-09-01T01:13:01Z",
        "body":"111",
        "author_association":"CONTRIBUTOR"
    },
    "repository":{
        "id":269285897,
        "node_id":"MDEwOlJlcG9zaXRvcnkyNjkyODU4OTc=",
        "name":"new",
        "full_name":"1kib/new",
        "owner":{
            "login":"1kib",
            "id":66345678,
            "node_id":"MDEyOk9yZ2FuaXphdGlvbjY2MzQ1Njc4",
            "avatar_url":"https://avatars3.githubusercontent.com/u/66345678?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/1kib",
            "html_url":"https://github.com/1kib",
            "followers_url":"https://api.github.com/users/1kib/followers",
            "following_url":"https://api.github.com/users/1kib/following{/other_user}",
            "gists_url":"https://api.github.com/users/1kib/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/1kib/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/1kib/subscriptions",
            "organizations_url":"https://api.github.com/users/1kib/orgs",
            "repos_url":"https://api.github.com/users/1kib/repos",
            "events_url":"https://api.github.com/users/1kib/events{/privacy}",
            "received_events_url":"https://api.github.com/users/1kib/received_events",
            "type":"Organization",
            "site_admin":false
        },
        "private":false,
        "html_url":"https://github.com/1kib/new",
        "description":"一个工具人仓库",
        "fork":false,
        "url":"https://api.github.com/repos/1kib/new",
        "forks_url":"https://api.github.com/repos/1kib/new/forks",
        "keys_url":"https://api.github.com/repos/1kib/new/keys{/key_id}",
        "collaborators_url":"https://api.github.com/repos/1kib/new/collaborators{/collaborator}",
        "teams_url":"https://api.github.com/repos/1kib/new/teams",
        "hooks_url":"https://api.github.com/repos/1kib/new/hooks",
        "issue_events_url":"https://api.github.com/repos/1kib/new/issues/events{/number}",
        "events_url":"https://api.github.com/repos/1kib/new/events",
        "assignees_url":"https://api.github.com/repos/1kib/new/assignees{/user}",
        "branches_url":"https://api.github.com/repos/1kib/new/branches{/branch}",
        "tags_url":"https://api.github.com/repos/1kib/new/tags",
        "blobs_url":"https://api.github.com/repos/1kib/new/git/blobs{/sha}",
        "git_tags_url":"https://api.github.com/repos/1kib/new/git/tags{/sha}",
        "git_refs_url":"https://api.github.com/repos/1kib/new/git/refs{/sha}",
        "trees_url":"https://api.github.com/repos/1kib/new/git/trees{/sha}",
        "statuses_url":"https://api.github.com/repos/1kib/new/statuses/{sha}",
        "languages_url":"https://api.github.com/repos/1kib/new/languages",
        "stargazers_url":"https://api.github.com/repos/1kib/new/stargazers",
        "contributors_url":"https://api.github.com/repos/1kib/new/contributors",
        "subscribers_url":"https://api.github.com/repos/1kib/new/subscribers",
        "subscription_url":"https://api.github.com/repos/1kib/new/subscription",
        "commits_url":"https://api.github.com/repos/1kib/new/commits{/sha}",
        "git_commits_url":"https://api.github.com/repos/1kib/new/git/commits{/sha}",
        "comments_url":"https://api.github.com/repos/1kib/new/comments{/number}",
        "issue_comment_url":"https://api.github.com/repos/1kib/new/issues/comments{/number}",
        "contents_url":"https://api.github.com/repos/1kib/new/contents/{+path}",
        "compare_url":"https://api.github.com/repos/1kib/new/compare/{base}...{head}",
        "merges_url":"https://api.github.com/repos/1kib/new/merges",
        "archive_url":"https://api.github.com/repos/1kib/new/{archive_format}{/ref}",
        "downloads_url":"https://api.github.com/repos/1kib/new/downloads",
        "issues_url":"https://api.github.com/repos/1kib/new/issues{/number}",
        "pulls_url":"https://api.github.com/repos/1kib/new/pulls{/number}",
        "milestones_url":"https://api.github.com/repos/1kib/new/milestones{/number}",
        "notifications_url":"https://api.github.com/repos/1kib/new/notifications{?since,all,participating}",
        "labels_url":"https://api.github.com/repos/1kib/new/labels{/name}",
        "releases_url":"https://api.github.com/repos/1kib/new/releases{/id}",
        "created_at":"2020-06-04T07:08:05Z",
        "updated_at":"2020-06-19T01:05:05Z",
        "pushed_at":"2020-06-19T01:05:03Z",
        "git_url":"git://github.com/1kib/new.git",
        "ssh_url":"git@github.com:1kib/new.git",
        "clone_url":"https://github.com/1kib/new.git",
        "svn_url":"https://github.com/1kib/new",
        "homepage":null,
        "size":9,
        "stargazers_count":0,
        "watchers_count":0,
        "language":null,
        "has_issues":true,
        "has_downloads":true,
        "has_wiki":true,
        "has_pages":false,
        "forks_count":1,
        "mirror_url":null,
        "open_issues_count":358,
        "forks":1,
        "open_issues":358,
        "watchers":0,
        "default_branch":"master"
    },
    "sender":{
        "login":"3ks",
        "id":32058002,
        "node_id":"MDQ6VXNlcjMyMDU4MDAy",
        "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
        "gravatar_id":"",
        "url":"https://api.github.com/users/3ks",
        "html_url":"https://github.com/3ks",
        "followers_url":"https://api.github.com/users/3ks/followers",
        "following_url":"https://api.github.com/users/3ks/following{/other_user}",
        "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
        "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
        "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
        "organizations_url":"https://api.github.com/users/3ks/orgs",
        "repos_url":"https://api.github.com/users/3ks/repos",
        "events_url":"https://api.github.com/users/3ks/events{/privacy}",
        "received_events_url":"https://api.github.com/users/3ks/received_events",
        "type":"User",
        "site_admin":false
    }
}
{% endcodeblock %}

和：

{% codeblock "pull_request_comment_payload.json" lang:json  >folded %}
{
    "action":"created",
    "issue":{
        "url":"https://api.github.com/repos/1kib/new/issues/1381",
        "labels_url":"https://api.github.com/repos/1kib/new/issues/1381/labels{/name}",
        "comments_url":"https://api.github.com/repos/1kib/new/issues/1381/comments",
        "events_url":"https://api.github.com/repos/1kib/new/issues/1381/events",
        "html_url":"https://github.com/1kib/new/pull/1381",
        "id":689730941,
        "node_id":"MDExOlB1bGxSZXF1ZXN0NDc2NzQ3MjU0",
        "number":1381,
        "title":"PR test",
        "user":{
            "login":"3ks",
            "id":32058002,
            "node_id":"MDQ6VXNlcjMyMDU4MDAy",
            "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/3ks",
            "html_url":"https://github.com/3ks",
            "followers_url":"https://api.github.com/users/3ks/followers",
            "following_url":"https://api.github.com/users/3ks/following{/other_user}",
            "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
            "organizations_url":"https://api.github.com/users/3ks/orgs",
            "repos_url":"https://api.github.com/users/3ks/repos",
            "events_url":"https://api.github.com/users/3ks/events{/privacy}",
            "received_events_url":"https://api.github.com/users/3ks/received_events",
            "type":"User",
            "site_admin":false
        },
        "labels":[
        ],
        "state":"open",
        "locked":false,
        "assignee":null,
        "assignees":[
        ],
        "milestone":null,
        "comments":2,
        "created_at":"2020-09-01T01:21:22Z",
        "updated_at":"2020-09-01T01:34:48Z",
        "closed_at":null,
        "body":"Test"
    },
    "comment":{
        "url":"https://api.github.com/repos/1kib/new/issues/comments/684138714",
        "html_url":"https://github.com/1kib/new/pull/1381#issuecomment-684138714",
        "issue_url":"https://api.github.com/repos/1kib/new/issues/1381",
        "id":684138714,
        "node_id":"MDEyOklzc3VlQ29tbWVudDY4NDEzODcxNA==",
        "user":{
            "login":"3ks",
            "id":32058002,
            "node_id":"MDQ6VXNlcjMyMDU4MDAy",
            "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/3ks",
            "html_url":"https://github.com/3ks",
            "followers_url":"https://api.github.com/users/3ks/followers",
            "following_url":"https://api.github.com/users/3ks/following{/other_user}",
            "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
            "organizations_url":"https://api.github.com/users/3ks/orgs",
            "repos_url":"https://api.github.com/users/3ks/repos",
            "events_url":"https://api.github.com/users/3ks/events{/privacy}",
            "received_events_url":"https://api.github.com/users/3ks/received_events",
            "type":"User",
            "site_admin":false
        },
        "created_at":"2020-09-01T01:34:47Z",
        "updated_at":"2020-09-01T01:34:47Z",
        "body":"11111",
        "author_association":"CONTRIBUTOR"
    },
    "repository":{
        "id":269285897,
        "node_id":"MDEwOlJlcG9zaXRvcnkyNjkyODU4OTc=",
        "name":"new",
        "full_name":"1kib/new",
        "owner":{
            "login":"1kib",
            "id":66345678,
            "node_id":"MDEyOk9yZ2FuaXphdGlvbjY2MzQ1Njc4",
            "avatar_url":"https://avatars3.githubusercontent.com/u/66345678?v=4",
            "gravatar_id":"",
            "url":"https://api.github.com/users/1kib",
            "html_url":"https://github.com/1kib",
            "followers_url":"https://api.github.com/users/1kib/followers",
            "following_url":"https://api.github.com/users/1kib/following{/other_user}",
            "gists_url":"https://api.github.com/users/1kib/gists{/gist_id}",
            "starred_url":"https://api.github.com/users/1kib/starred{/owner}{/repo}",
            "subscriptions_url":"https://api.github.com/users/1kib/subscriptions",
            "organizations_url":"https://api.github.com/users/1kib/orgs",
            "repos_url":"https://api.github.com/users/1kib/repos",
            "events_url":"https://api.github.com/users/1kib/events{/privacy}",
            "received_events_url":"https://api.github.com/users/1kib/received_events",
            "type":"Organization",
            "site_admin":false
        },
        "private":false,
        "html_url":"https://github.com/1kib/new",
        "description":"一个工具人仓库",
        "fork":false,
        "url":"https://api.github.com/repos/1kib/new",
        "forks_url":"https://api.github.com/repos/1kib/new/forks",
        "keys_url":"https://api.github.com/repos/1kib/new/keys{/key_id}",
        "collaborators_url":"https://api.github.com/repos/1kib/new/collaborators{/collaborator}",
        "teams_url":"https://api.github.com/repos/1kib/new/teams",
        "hooks_url":"https://api.github.com/repos/1kib/new/hooks",
        "issue_events_url":"https://api.github.com/repos/1kib/new/issues/events{/number}",
        "events_url":"https://api.github.com/repos/1kib/new/events",
        "assignees_url":"https://api.github.com/repos/1kib/new/assignees{/user}",
        "branches_url":"https://api.github.com/repos/1kib/new/branches{/branch}",
        "tags_url":"https://api.github.com/repos/1kib/new/tags",
        "blobs_url":"https://api.github.com/repos/1kib/new/git/blobs{/sha}",
        "git_tags_url":"https://api.github.com/repos/1kib/new/git/tags{/sha}",
        "git_refs_url":"https://api.github.com/repos/1kib/new/git/refs{/sha}",
        "trees_url":"https://api.github.com/repos/1kib/new/git/trees{/sha}",
        "statuses_url":"https://api.github.com/repos/1kib/new/statuses/{sha}",
        "languages_url":"https://api.github.com/repos/1kib/new/languages",
        "stargazers_url":"https://api.github.com/repos/1kib/new/stargazers",
        "contributors_url":"https://api.github.com/repos/1kib/new/contributors",
        "subscribers_url":"https://api.github.com/repos/1kib/new/subscribers",
        "subscription_url":"https://api.github.com/repos/1kib/new/subscription",
        "commits_url":"https://api.github.com/repos/1kib/new/commits{/sha}",
        "git_commits_url":"https://api.github.com/repos/1kib/new/git/commits{/sha}",
        "comments_url":"https://api.github.com/repos/1kib/new/comments{/number}",
        "issue_comment_url":"https://api.github.com/repos/1kib/new/issues/comments{/number}",
        "contents_url":"https://api.github.com/repos/1kib/new/contents/{+path}",
        "compare_url":"https://api.github.com/repos/1kib/new/compare/{base}...{head}",
        "merges_url":"https://api.github.com/repos/1kib/new/merges",
        "archive_url":"https://api.github.com/repos/1kib/new/{archive_format}{/ref}",
        "downloads_url":"https://api.github.com/repos/1kib/new/downloads",
        "issues_url":"https://api.github.com/repos/1kib/new/issues{/number}",
        "pulls_url":"https://api.github.com/repos/1kib/new/pulls{/number}",
        "milestones_url":"https://api.github.com/repos/1kib/new/milestones{/number}",
        "notifications_url":"https://api.github.com/repos/1kib/new/notifications{?since,all,participating}",
        "labels_url":"https://api.github.com/repos/1kib/new/labels{/name}",
        "releases_url":"https://api.github.com/repos/1kib/new/releases{/id}",
        "created_at":"2020-06-04T07:08:05Z",
        "updated_at":"2020-06-19T01:05:05Z",
        "pushed_at":"2020-09-01T01:21:22Z",
        "git_url":"git://github.com/1kib/new.git",
        "ssh_url":"git@github.com:1kib/new.git",
        "clone_url":"https://github.com/1kib/new.git",
        "svn_url":"https://github.com/1kib/new",
        "homepage":null,
        "size":9,
        "stargazers_count":0,
        "watchers_count":0,
        "language":null,
        "has_issues":true,
        "has_downloads":true,
        "has_wiki":true,
        "has_pages":false,
        "forks_count":1,
        "mirror_url":null,
        "open_issues_count":359,
        "forks":1,
        "open_issues":359,
        "watchers":0,
        "default_branch":"master"
    },
    "sender":{
        "login":"3ks",
        "id":32058002,
        "node_id":"MDQ6VXNlcjMyMDU4MDAy",
        "avatar_url":"https://avatars3.githubusercontent.com/u/32058002?v=4",
        "gravatar_id":"",
        "url":"https://api.github.com/users/3ks",
        "html_url":"https://github.com/3ks",
        "followers_url":"https://api.github.com/users/3ks/followers",
        "following_url":"https://api.github.com/users/3ks/following{/other_user}",
        "gists_url":"https://api.github.com/users/3ks/gists{/gist_id}",
        "starred_url":"https://api.github.com/users/3ks/starred{/owner}{/repo}",
        "subscriptions_url":"https://api.github.com/users/3ks/subscriptions",
        "organizations_url":"https://api.github.com/users/3ks/orgs",
        "repos_url":"https://api.github.com/users/3ks/repos",
        "events_url":"https://api.github.com/users/3ks/events{/privacy}",
        "received_events_url":"https://api.github.com/users/3ks/received_events",
        "type":"User",
        "site_admin":false
    }
}
{% endcodeblock %}


通过对比工具对比两者的内容，发现只能通过 payload 中的 URL 来区分两者。

可用的参数有：`issue.url` 和 `issue.html_url`（ pr comment 也是这个参数），两者都是一个 URL 链接，包含关键字 `issues` 则表示 issue 的 comment，包含关键字 `pull` 则表示 pr 的 comment
