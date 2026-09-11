# Hermes Agent セットアップ＆設定ガイド

このリポジトリには、LaTeX 執筆を AI エージェント（Hermes Agent）で支援する環境が同梱されています。
使わなくても LaTeX 執筆自体には影響しません。

- **Hermes Agent** — 高性能な自律型 AI エージェント。Dev Container ビルド時にインストール済み。
- **VS Code ACP Client** — エディタ内から Hermes Agent を呼び出してタスクを実行できる拡張機能。

設定やセッション履歴は Docker named volume（`setting-latex-hermes` → `/root/.hermes`）に保存されるため、**コンテナを再構築しても設定内容は保持されます**。

---

## 1. クイックスタート

Dev Container のビルド時に Hermes Agent は自動インストールされています。
**ターミナルで `hermes model` を実行してモデルを設定するだけですぐに動きます。**

```bash
hermes model    # プロバイダ / モデル / APIキーの設定（これだけで利用可能になります）
hermes          # 対話開始（CLI）
```

### 1.1 初期設定の流れ

1. VS Code 内のターミナル（`Ctrl + \`` またはメニュー「ターミナル」→「新しいターミナル」）を開きます。
2. `hermes model` を実行します。
3. プロバイダ（OpenRouter, Anthropic, OpenAI, DeepSeek, Custom など）を選択し、モデル名と API キーを入力します。
   - 入力した API キーは `~/.hermes/.env` に、モデル設定は `~/.hermes/config.yaml` に自動保存されます。
4. 設定完了後、`hermes` を実行すると対話が始まります。

### 1.2 VS Code（ACP Client）からの利用

Dev Container には **ACP Client** 拡張機能（`formulahendry.acp-client`）がプリインストールされています。
モデル設定完了後、VS Code のエージェント機能 / ACP Client から Hermes Agent を選択してエディタ内で執筆や推敲、エラー修正のタスクを依頼できます。

### 1.3 動作確認

設定が正しく行われているかは `hermes doctor` で診断できます。

```bash
hermes doctor   # 環境・設定・依存関係の診断
```

---

## 2. 日常的な設定・コマンド一覧

### 2.1 よく使うコマンド

| コマンド | 説明 |
|---|---|
| `hermes model` | プロバイダ / モデルの切り替え・再設定 |
| `hermes` (または `hermes chat`) | CLI で対話セッションを開始 |
| `hermes setup` | 対話式セットアップウィザード（APIキー等の一括設定） |
| `hermes doctor` | 設定・依存関係の健康診断 |
| `hermes config` | 現在の設定一覧を表示 |
| `hermes config set KEY VAL` | 設定値を更新（APIキーは自動で `.env` へ、その他は `config.yaml` へ保存） |
| `hermes config get KEY` | 指定したキーの設定値を確認 |
| `hermes update` | Hermes Agent 本体の更新 |

### 2.2 一時的なモデル指定・セッション操作

```bash
# 特定のモデルで一時起動（設定ファイルは変更しない）
hermes chat --model anthropic/claude-sonnet-4

# 前回のセッションを再開
hermes --continue
```

**セッション内の主なスラッシュコマンド:**
- `/new` — 会話をリセットして新規セッションを開始
- `/model` — セッション内でモデルを変更
- `/clear` — 画面をクリア

---

## 3. 設定の構造とファイル配置

### 3.1 本環境（Dev Container）での配置

設定やデータはすべて **Hermes Home**（`/root/.hermes/`）配下にまとめられています。
Docker named volume により永続化されているため、コンテナの停止や再ビルドでも消えません。

| 項目 | パス | 役割 |
|---|---|---|
| 設定ファイル | `/root/.hermes/config.yaml` | 一般設定（モデル、ターミナル、表示など） |
| 秘密情報ファイル | `/root/.hermes/.env` | APIキーやシークレット（パーミッション 600） |
| SOUL 設定 | `/root/.hermes/SOUL.md` | エージェントの基本振る舞い・システムプロンプト |
| スキル | `/root/.hermes/skills/` | エージェントの拡張スキル |
| 会話ログ・セッション | `/root/.hermes/sessions/`, `state.db` | 会話履歴・SQLite 状態ストア |
| 実行バイナリ | `/usr/local/bin/hermes` | Hermes CLI 本体 |

### 3.2 `config.yaml` と `.env` の分離ルール

Hermes Agent は「**一般設定 (`config.yaml`)**」と「**秘密情報 (`.env`)**」を明確に分けて管理します。

- **API キーやトークンなどの秘密情報は `config.yaml` に直書きしない**:
  `hermes config set KEY VAL` コマンドを使用すれば、API キーは自動で `.env` に、通常の設定値は `config.yaml` に振り分けられます。
- パスを確認したい場合:
  ```bash
  hermes config path       # config.yaml の絶対パスを表示
  hermes config env-path   # .env の絶対パスを表示
  ```

### 3.3 設定の優先順位

上にあるものが優先して適用されます。

```text
1. CLI 引数 (--model など)
2. config.yaml (~/.hermes/config.yaml)
3. .env (環境変数フォールバック)
4. 組み込みデフォルト値
```

---

## 4. 詳細設定リファレンス

### 4.1 `config.yaml` の主要セクション

| セクション | 主なキー | 説明 |
|---|---|---|
| `model` | `default`, `provider`, `base_url`, `api_key`, `api_mode` | 使用するモデルやプロバイダの設定 |
| `agent` | `max_turns`, `tool_use_enforcement` | 自律実行の最大ターン数など |
| `terminal` | `backend` (local/docker), `timeout` | コマンド実行ターミナルの動作 |
| `compression` | `enabled`, `threshold`, `target_ratio` | コンテキスト圧縮の挙動 |
| `display` | `skin`, `interface` (cli/tui), `language` | CLI 表示テーマや言語 |
| `approvals` | `mode` (smart/manual/off) | ツール実行の承認モード |
| `skills` | スキルの管理設定 | スキル読み込み設定 |

設定変更の例:
```bash
# ターミナル実行バックエンドを local に設定
hermes config set terminal.backend local

# APIキーを更新
hermes config set OPENROUTER_API_KEY sk-or-xxxx
```

### 4.2 `.env` での環境変数利用

`config.yaml` 内で `${ENV_VAR}` 形式で環境変数を参照することも可能です。

```yaml
model:
  default: deepseek-chat
  provider: custom
  base_url: https://api.deepseek.com/v1/
  api_key: ${HERMES_CUSTOM_API_DEEPSEEK_COM_API_KEY}
  api_mode: chat_completions
```

---

## 5. トラブルシューティング

| 症状 | 対処方法 |
|---|---|
| モデルが動かない / API エラーが出る | `hermes model` を再実行してプロバイダ、モデル名、API キーを再設定してください。 |
| 設定ファイルの状態を確認したい | `hermes doctor` を実行して診断結果を確認してください。設定の差分がある場合は `hermes config check` や `hermes config migrate` で更新できます。 |
| 設定を手動編集して崩れてしまった | `hermes config edit` または `nano ~/.hermes/config.yaml` で構文（インデント等）を確認するか、`hermes config check` で検証してください。 |
| 再起動後に設定が引き継がれているか確認したい | `hermes config get model` で現在のモデル設定が表示されれば永続化されています。 |

---

## 6. 参考リンク

- [Hermes Agent 公式ドキュメント](https://hermes-agent.nousresearch.com/)
- [NousResearch/hermes-agent (GitHub)](https://github.com/NousResearch/hermes-agent)
- [Hermes Agent 設定ガイド (公式)](https://hermes-agent.nousresearch.com/docs/user-guide/configuration)
- [ACP Client（VS Code 拡張）](https://marketplace.visualstudio.com/items?itemName=formulahendry.acp-client)
