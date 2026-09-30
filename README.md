# gw

git worktree ヘルパー。ブランチ名だけで worktree の作成・移動・削除を行い、リポジトリローカルの設定ファイル（`.claude/settings.local.json` など）を worktree 間でシンボリックリンク共有します。

## インストール

```bash
brew tap tk568/tap
brew install gw
```

`.zshrc` に以下を追加します（`cd` を伴うため、シェル関数ラッパーの読み込みが必要です）。

```zsh
eval "$(gw init zsh)"
```

zsh 補完は Homebrew の `site-functions` 経由で自動的に有効になります。

## 使い方

```bash
gw switch -c <branch>   # worktree を作成してブランチに切り替え（既存なら移動のみ）
gw switch <branch>      # 既存 worktree に移動
gw remove <branch>      # worktree を削除してメインに戻る
gw list                 # worktree のブランチ一覧
gw migrate              # 既存 worktree にローカルファイルのシンボリックリンクを張る
```

worktree は `<リポジトリ名>-<ブランチ名>` の名前でリポジトリと同じ階層に作成されます（ブランチ名の `/` は `-` に変換）。

`gw switch -c` は既存ブランチにも対応しており、remote に同名ブランチがあれば追従します（local が先行している場合は保持）。

## worktree 間で共有するローカルファイル

git 管理外のリポジトリローカルファイルを、worktree 作成時にメイン worktree からシンボリックリンクします。対象は `git config` で指定します（コロン区切り）。

```bash
# 全リポジトリ共通
git config --global gw.local-files ".claude/settings.local.json:mise.toml"

# リポジトリ個別（global より優先）
git config gw.local-files ".claude/settings.local.json:mise.toml:.env.myproject"
```

そのブランチで git が追跡しているファイルはリンクしません（`skip (tracked)` と出力して飛ばします）。リンクに置き換えると `git status` に typechange が出て rebase が止まるためです。

## 動作要件

- git 2.x（`git worktree` が必要）
- bash / zsh
