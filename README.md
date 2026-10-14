# skills

普段使いしている Claude Code の skill（Agent Skills / `SKILL.md` 形式）を置いています。

## 入れ方

```sh
npx skills add maee-co/skills                      # 全部
npx skills add maee-co/skills --skill deep-audit   # 1 本だけ
```

更新は `npx skills update`。

手で入れる場合は `skills/<name>/` を `~/.claude/skills/` にコピーすれば動きます。

## 一覧

| skill | 何をするか |
|---|---|
| [close](skills/close/SKILL.md) | 「しめよう」で、締めてよいかを git / gh の事実で判定し、残件を片づけてから報告する |
| [deep-audit](skills/deep-audit/SKILL.md) | セキュリティ監査（`--security`）と原因不明バグ（`--bug`）を、壊す側から逆向きに詰める |

## License

MIT
