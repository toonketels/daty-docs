---
title: "Code Review"
description: ""
lead: ""
date: 2022-09-16T08:16:02+03:00
lastmod: 2022-09-16T08:16:02+03:00
draft: true
images: []
menu:
  docs:
    parent: "backend"
    identifier: "code-review-dec46dbaa4a89fa3cfd3ed58fa75d215"
weight: 999
toc: TOC
---


## Summary

| Prefix |                     | Description                                                     | Contract    |
| -------|---------------------|-----------------------------------------------------------------|-------------|
| R      | Required            | Change the code to meet quality standards                       | req CC      |
| NTH    | Nice To Have        | Minor improvement to meet quality standards                     | opt CC      |
| PP     | Personal Preference | Alternative implementation suggested                            | opt CC      |
| CS     | Code Style          | Not in line with team's code style                              | req CC      |
| ++     | Plus 1              | Compliments quality code                                        | None        |
| Q      | Question            | Asking clarification                                            | req Comment | 


Contract
- req CC     : required Code Change 
- opt CC     : optional Code Change
- None       : No action needed
- req Comment: required response to comment 



## Description

### R: Required
- A change deemed required to meet the team’s quality standards. 
- Contributor needs to update the code. 
- Contributor responds to comment with the id of commit.
- He can push back on the changes if he replies with a strong motivation for doing so, which happened occasionally.

### NTH: Nice To Have
- A minor improvement the reviewer would like to see
- Up to the contributor to make the change depending on his time constraints
- If code change made, responds to comment with the id of commit

### PP: Personal Preference
- The reviewer offers an alternative, usually explaining the tradeoffs
- Instrumental in coaching developers to extend their coding repertoire
- Up to the contributor to adopt it
- If code change made, responds to comment with the id of commit

### CS: Code Style
- Easy to make changes about patterns the team agreed upon
- Links to the code style document for the contributor to review
- Contributor responds to comment with the id of commit
- The contributor needs to make the change

### ++: Plus 1
- Compliments quality code
- Reinforces best practices and celebrates eloquent snippets. 
- Not only does it motivates the contributor, but it also teaches other reviewers good coding patterns. 
- No action is needed.

### Q: Question
- The reviewer wants more information. 
- Usually, to ask why the contributor coded it in this particular way. 
- Often leads to a small discussion.
- The contributor needs to provide an answer.


## Flow

1. Open PR:
    1. Reviewee opens PR in bitbucket
    2. Creates a message in slack (PR announcement):
        1. Tags reviewers 
        2. Links to PR
2. Reviewers start a round of reviewing
    1. Prefixes all comments with R, NTH, PP, CS, ++, Q
    2. When done, leaves comment on the PR announcement in slack
3. Reviewee starts a round of changes
    1. Only when all reviewers have left a message in slack
    2. Addresses all comments acoriding to contract
    3. After all comments have been addressed, notificies reviewers in slack 
4. New round of reviewing starts, etc, until approval PR
5. Reviewee merges the PR 