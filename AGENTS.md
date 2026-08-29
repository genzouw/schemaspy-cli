# AGENTS.md — AI コーディングエージェント向けポリシー

本ドキュメントは、自律型コーディングエージェント (Jules / Devin / Codex / Claude Code / GitHub Copilot / Cursor / Cline / Windsurf / Aider / Sweep / PR-Agent 等) が、公開 OSS リポジトリ [`genzouw/schemaspy-cli`](https://github.com/genzouw/schemaspy-cli) で作業し Pull Request を作成するときに **必ず守るべき規範** を定義します。
キーワードの解釈は [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) ([日本語訳](https://www.nic.ad.jp/ja/tech/ipa/RFC2119JA.html)) に従います (MUST / MUST NOT / SHOULD / SHOULD NOT / MAY)。

このファイルは [agents.md 規格](https://agents.md/) に従って配置しています。Jules や Codex などはこのファイルをリポジトリルートから自動的に読み込みます。それ以外のエージェント (Aider は `.aider.conf.yml`、Gemini CLI は `.gemini/settings.json` 等) では、`AGENTS.md` を読み込むよう別途設定が必要な場合があるため、利用するエージェントのドキュメントを確認してください。

---

## 1. 最重要原則: 公開 OSS で完全無料の SaaS・AI・ツールのみを利用する

本リポジトリの CI/CD および自動化ワークフローでは、**公開 OSS リポジトリ向けに「完全無料で利用可能」な SaaS / AI / ツールのみ** を利用します。
「完全無料」の定義は、以下のすべてを満たすことです。

1. 公開 OSS リポジトリ (パブリックな GitHub リポジトリ) で利用する場合に、課金が一切発生しないこと
2. 利用にあたって従量課金 API キー / トークンを必要としないこと
3. 利用量・レート制限・期間制限を超えた瞬間に課金が始まる "無料枠" 型でないこと
4. 有料プラン / 有料ライセンス / 有料トライアル / シート課金 / クレジットカード登録を必要としないこと

上記をひとつでも満たさないサービスを組み込む PR は **MUST NOT** で、提出された場合は内容の良し悪しにかかわらずリポジトリオーナーによってクローズされます (現時点で自動クローズを行う仕組みは導入されていません)。

### 1.1 MUST NOT — これらを含む PR は問答無用でクローズされます

- **MUST NOT**: LLM プロバイダの API キー / トークンを GitHub Secrets に登録し、CI ワークフロー / GitHub Action から呼び出す構成の追加。
  - 該当する API キー例 (これらに限らない):
    - `GEMINI_API_KEY`, `GOOGLE_API_KEY`, `GOOGLE_GENERATIVE_AI_API_KEY`
    - `OPENAI_API_KEY`, `OPENAI_API_BASE_URL`
    - `ANTHROPIC_API_KEY`, `CLAUDE_API_KEY`, `CLAUDE_CODE_OAUTH_TOKEN`
    - `MISTRAL_API_KEY`, `COHERE_API_KEY`, `GROQ_API_KEY`
    - `DEEPSEEK_API_KEY`, `PERPLEXITY_API_KEY`, `XAI_API_KEY`, `TOGETHER_API_KEY`
    - `HUGGINGFACE_API_TOKEN` (推論 API として使う場合)
  - **「無料枠内に収まる前提」での利用も MUST NOT です**。レート制限到達時に課金が始まる構造そのものを禁止しています。
  - **OpenAI 互換エンドポイント経由 (`OPENAI_API_BASE_URL` を Gemini や DeepSeek 等に向けるパターン) も同じく MUST NOT** です。鍵の名称ではなく「課金可能な API へ繋がる鍵を登録する行為」を禁止しています。
- **MUST NOT**: 従量課金の外部 API キーを要する検索・スクレイピング系サービスの CI 組み込み (`TAVILY_API_KEY`, `EXA_API_KEY`, `SERPAPI_KEY`, `BRAVE_API_KEY` 等)。
- **MUST NOT**: 有料プラン / 有料ライセンス / 有料トライアル / クレジットカード登録を必要とするサービスの CI 組み込み。
- **MUST NOT**: 公開 OSS リポジトリでも Pro プラン以上を要求する SaaS の追加。
- **MUST NOT**: リポジトリオーナーに新規 Secret の発行・登録を依頼する PR (OIDC / 公開鍵証明を用いず、人手で鍵を回す構成のもの)。
- **MUST NOT**: 既存テスト / lint / セキュリティスキャンをスキップ / 無効化 / コメントアウトして提出すること。
- **MUST NOT**: 既に本リポジトリに導入済みのツールと機能が重複する追加 (`.github/workflows/` 配下を必ず事前確認すること)。
- **MUST NOT**: サードパーティ GitHub Action をタグ参照 (`@v1` 等) のみで導入すること。**フルコミット SHA で pin** してください。

### 1.2 SHOULD — 強く推奨される慣行

- **SHOULD**: 公式 (GitHub / OpenSSF / 主要 OSS 組織) または Verified creator の発行元から提供される GitHub Action / GitHub App を優先する。
- **SHOULD**: ワークフローの `permissions:` は最小権限から始める (`contents: read` を既定とし、必要なジョブで個別に書込権限を付与)。
- **SHOULD**: ワークフローに `concurrency:` を設定し、同一 PR / ref への多重起動を抑止する。
- **SHOULD**: 判断に迷う場合は PR を作らず、Issue でリポジトリオーナー (@genzouw) に相談する。

### 1.3 MAY — 採用してよい構成

- **MAY**: GitHub Marketplace の「公開 OSS リポジトリ向け完全無料プラン」で提供される Action / App。
- **MAY**: GitHub App の「公開 OSS リポジトリ向け完全無料枠」で、API キーの登録が不要なもの (例: CodeRabbit の OSS 無料枠)。
- **MAY**: 完全無料で配布されている GitHub Action (Marketplace 登録の有無は問わない)。
- **MAY**: ローカル LLM (Ollama / llama.cpp 等) を標準 GitHub-hosted runner 上で動作させる、Secrets 不要の自動化 (larger runner は public リポジトリでも課金対象となるため対象外)。
- **MAY**: リポジトリ内で完結するスクリプト / Make ターゲット (外部 SaaS 連携を伴わないもの)。
- **MAY**: 既存ワークフローのキャッシュ最適化、並列化、Action の SHA pin 更新といった、課金を伴わない構造改善。

### 1.4 ローカル環境と CI の区別

本ポリシーが禁止しているのは **CI/CD および自動化ワークフローへの組み込み** です。
開発者個人のローカル環境で、自分のアカウント・自分の負担で AI ツール (Claude Code / Cursor / Gemini CLI 等) を使うことは **MAY** です。

`ANTHROPIC_API_KEY` / `GEMINI_API_KEY` などを自分のシェルの環境変数として `export` して使うことは **MAY** です。
一方、同じ鍵を GitHub Secrets へ登録し CI から参照することは **MUST NOT** です。

---

## 2. PR を作成する前のチェックリスト (MUST すべて満たす)

- [ ] 追加するサービスが「公開 OSS リポジトリで完全無料で利用可能」であることを、**公式の料金ページ / ドキュメントの URL** で証明している。
- [ ] LLM プロバイダの API キー / 従量課金 API キーを GitHub Secrets に追加していない。`GEMINI_API_KEY` / `OPENAI_API_KEY` / `ANTHROPIC_API_KEY` 等を `secrets.*` から参照する記述が新規ファイルに含まれていない。
- [ ] 「無料枠内に収まる前提」の利用ではなく、「課金が一切発生しない構成」であることを PR 本文に明記している。
- [ ] 追加する GitHub Action は **フルコミット SHA で pin** している。
- [ ] `.github/workflows/` 配下の既存ワークフローと機能が重複していないことを確認した。
- [ ] 既存テスト / lint / セキュリティスキャンをスキップ・削除していない。
- [ ] リポジトリオーナーへ新規 Secret の登録を依頼していない。依頼が必要なら PR ではなく Issue で提案している。

## 3. PR 本文に必ず含めるべき情報

PR 説明文には以下を **MUST** で、**日本語で** 含めてください。

1. **目的**: この変更で何を改善したいか (1〜3 文)。
2. **変更内容**: 追加・更新・削除するファイルの一覧。
3. **「公開 OSS で完全無料」の証明**: 公式の料金ページ URL と、無料で利用できる条件の引用。外部サービスを一切追加しない変更ではその旨を明記。
4. **既存ツールとの重複がないことの確認**。
5. **マージ前に必要な手動セットアップ手順**: リポジトリ設定変更や App のインストールが必要なら、オーナーがそのまま実行できる粒度の番号付き手順で書く。
6. **想定リスクとロールバック手順**。
7. **動作確認結果**: ローカルで実行したコマンドとその結果。

## 4. 言語規約

- PR タイトル、PR 説明文、コミットメッセージ、ソースコード内コメント、ドキュメントは **日本語** で記載する。
- 技術用語 (GitHub Actions / CI/CD / API キー / Marketplace 等) と識別子は原語のままでよい。
- コミットメッセージおよび PR タイトルは [Conventional Commits](https://www.conventionalcommits.org/ja/v1.0.0/) に従う。

## 5. 例外申請プロセス (SHOULD)

本ポリシーから外れる導入を検討したい場合は、PR を作成する **前に** Issue で提案し、リポジトリオーナー (@genzouw) の明示的な承認を **SHOULD** 取得してください。承認のない有料サービス導入 PR はリポジトリオーナーによってクローズされます (現時点で自動クローズを行う仕組みは導入されていません)。
Issue では「なぜ既存の無料サービスでは目的を達成できないか」「課金リスクをどう管理するか」を明確に書いてください。

「とりあえず PR を作って判断してもらう」というアプローチは取らないでください。レビューコストが発生し、結果として全員の生産性を下げます。

## 6. 判断に迷ったときのフローチャート

迷ったら次の順番で安全側に倒してください。

1. 課金が一切発生しないことに 100% の確信が持てない → PR を作らずに Issue で提案する。
2. 既存ツールと重複しているか判断できない → PR を作らずに Issue で提案する。
3. 新規 Secrets / 追加権限が必要である → PR を作らずに Issue で提案する。

## 7. 本ポリシーの適用範囲

本ポリシーは [@genzouw](https://github.com/genzouw) が公開しているすべての公開リポジトリに共通で適用されます。
