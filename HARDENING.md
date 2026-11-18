<!-- markdownlint-disable -->

# Hardening Report: nexterias--actions-vercel/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nexterias--actions-vercel/v2.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed CLAUDE.md without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/CLAUDE.md b/CLAUDE.md
deleted file mode 120000
index 55bf822..0000000
--- a/CLAUDE.md
+++ /dev/null
@@ -1 +0,0 @@
-./AGENTS.md
\ No newline at end of file
diff --git a/CLAUDE.md b/CLAUDE.md
new file mode 100644
index 0000000..c2ad42d
--- /dev/null
+++ b/CLAUDE.md
@@ -0,0 +1,33 @@
+# AGENTS.md for `nexterias/actions-vercel`
+
+## Commands
+
+```sh
+# 型チェック
+pnpm lint
+
+# フォーマット確認
+pnpm fmt:check
+```
+
+## 開発ルール
+
+- ソースコードを変更した場合、`pnpm lint && pnpm fmt:check`が必ず通過することを確認する
+- `src/`配下のソースコードを変更した場合、`docs/`配下のドキュメントを更新する必要があるか確認し、ユーザーの指示を仰ぐこと
+
+## コミットメッセージ
+
+- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)の規約に従う
+- コミットメッセージは英語で記述すること
+
+### テンプレート
+
+```
+<type>[optional scope]: <description>
+
+[optional body]
+
+[optional footer(s)]
+```
+
+使用できる`type`は、`feat`、`fix`、`docs`、`perf`、`refactor`、`style`、`test`、`chore`とすること

```

Locations:

- `CLAUDE.md`

## Iteration Notes

### Iteration 1

**Fixes applied:** prior-fix

**Notes:**

I tried to redo the previous fix by writing CLAUDE.md as a regular file (mode 0644, 862 bytes) with the same content as AGENTS.md, which is what that fix contained. It may not have worked. When I listed the directory afterwards, CLAUDE.md still showed up as a symlink to ./AGENTS.md (mode 0777). The write may have gone through the symlink into AGENTS.md instead of replacing it. AGENTS.md's content is unchanged either way, because the text I wrote is identical to what it already held. If the symlink is still there, it has to be deleted and replaced with a regular file, and I had no tool that could delete it. Please check whether CLAUDE.md is a symlink or a regular file before relying on this fix. No other files were changed.

