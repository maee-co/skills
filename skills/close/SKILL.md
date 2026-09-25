---
name: close
description: Use when ユーザーが「しめよう」「終わろう」「今日はここまで」と締めを告げたとき — 締めてよいかを git / gh の事実で判定し、残件は処理してから報告する。
user-invocable: true
allowed-tools:
  - Bash(git:*)
  - Bash(gh:*)
---

# /close

「しめよう」は**判断ではなく起動語**。言った人はセッションの中身を読み返していない。だから締めてよいかを判定するのは AI 側で、材料は機械が持っている事実だけにする。

**「終了しますか？」と聞き返さない。** 判断を相手に戻すと、相手が点検しに来ることになる — この skill が消したい手間そのもの。

## 1. 事実を採る

cwd だけでなく **worktree 全本**を見る。作業は cwd 以外のツリーにも散る。

```sh
git worktree list --porcelain
# 各ツリーで
git -C <path> status --porcelain          # 未 commit
git -C <path> log --oneline @{u}..HEAD    # 未 push（upstream 未設定なら別途 `git log --oneline origin/main..HEAD`）
# PR
gh pr list --author @me --state open --json number,headRefName,isDraft,mergeStateStatus,autoMergeRequest
```

## 2. 帰属を推測しない

並走セッションがあると、他人の worktree や PR が混ざる。

- 自分のもの = このセッションで作ったブランチ / cwd のツリー
- 判らないものは自分にも他人にも数えず、**「帰属未確認」として別枠で出す**。黙って落とすと「見えないから無い」になる
- 触る前に `gh pr view <N> --json headRefName,author` で 1 回確かめる

## 3. 残件は自分で処理する

出た残件は報告する前に片づける。相手に渡すのは処理した結果か、Issue 番号だけ。

| 残件 | 手 |
|---|---|
| 未 push の commit | push して PR。PR にするほどでなければ理由を 1 行 |
| PR が BEHIND | `gh pr update-branch <N>` |
| auto-merge 未設定（使っている repo なら） | `gh pr merge --auto --squash <N>` |
| コンフリクト | その場で解く。内容の判断が要って解けないなら Issue に積んで番号を出す |
| 使い終わった worktree | `git worktree remove <path>` → `git branch -D <branch>` |
| 未 commit | 調査だけのセッションなら残してよい。事実として 1 行出すだけ |
| gh が取れない | 「未確認」と書く。**取れなかったことを「残件ゼロ」と書かない** |

## 4. 出し方

1 ブロックで出す。

- 締めてよいなら「締めてよい」と断定で始め、根拠の数字を添える（例: ツリー 2 本・未 push 0・自分の open PR 1 本 auto-merge=on）
- 残件があったなら、処理済みか番号付きかのどちらかにしてから出す
- セッションの要約は書かない。書くのは締め可否と残件だけ
