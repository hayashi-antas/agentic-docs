<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 最近のAGENTS.mdの書き方ベスト実例

結論だけ言うと、**最近のAGENTS.mdは「短めの README for Agents」を、レイヤー別（グローバル／プロジェクト／フォルダ）に用意する書き方がベストプラクティス**になっています。[^1][^2]
中身はだいたい「概要・技術スタック・コマンド・構成・コーディング規約・テスト・AIへの挙動指示」という7ブロックが定番です。[^3][^4][^5]

以下、「そのままコピペして少し書き換えれば使える」最近っぽい実例を3つ出します（プロジェクト用／フォルダ専用／グローバル）。

***

## 共通の書き方のポイント

- **ツール非依存で書く**（Codex / Cursor / Copilot / Jules 等どれでも読める前提で、特定製品名は最小限）。[^6][^1]
- セクション構成はだいたい固定：

1. Project Overview
2. Tech Stack
3. Essential Commands
4. Structure \& Architecture
5. Code Style \& Conventions
6. Testing Requirements
7. AI Agent Guidelines（ここに「こう動いてほしい」を書く）[^4][^5][^3]
- 長文で歴史を書くより、「AIがすぐ行動に使える情報」を箇条書きで短く書くほうが、最近のガイドでも推奨されています。[^5][^3]

***

## プロジェクト用：汎用Webアプリ向けテンプレ

シングルリポジトリ or 代表的な1サービス用の、いまっぽい構成です。[^3][^5]

```markdown
# AGENTS for This Project

## 1. Project Overview

- This is a web application for {目的を一行で}.
- Primary goals: {例: manage reading logs, review OCR results, internal admin dashboard}.

## 2. Tech Stack

- Backend: {Ruby on Rails 8.1 / Node.js 22 / etc.}
- Frontend: {React 18 + TypeScript / Next.js 15 / Vue 3, etc.}
- Database: {PostgreSQL 16}
- Infra / Services: {Redis, Sidekiq, S3, etc.}

## 3. Essential Commands

- Setup: `{セットアップコマンド}`
- Dev server: `{開発サーバ起動コマンド}`
- Tests: `{テストコマンド (unit / e2e など) }`
- Lint / Format: `{lint / format コマンド}`

Always propose and, when allowed, run the relevant test and lint commands
after making code changes.

## 4. Structure & Architecture

- Main app code: `{src/ or app/} ディレクトリの簡単な説明`.
- `{例: app/services}`: business logic lives here, keep controllers thin.
- `{例: frontend/app}`: use React Server Components by default; opt-in to `"use client"`.

When adding new files, follow existing directory patterns and naming.

## 5. Code Style & Conventions

- Language: {TypeScript only / Ruby only / mixed but prefer X for new code}.
- Follow existing naming patterns in each directory before introducing new ones.
- Prefer small, composable functions; avoid god objects / god components.

If unsure, inspect similar existing files and mirror their style.

## 6. Testing Requirements

- Any behavior change must be covered by tests (new or updated).
- For bugs: add a regression test that fails before the fix and passes after.
- Do not delete tests unless explicitly instructed.

## 7. AI Agent Guidelines

- Preserve public APIs and external behavior unless the task says otherwise.
- Before large refactors, summarize the planned changes in markdown comments.
- Prefer minimal diffs that solve the task cleanly over large rewrites.
- If requirements are ambiguous, ask clarifying questions instead of guessing.
```

このレベルで「余計な歴史話を省き、AIがすぐ使える情報だけを箇条書きにする」のが、Codex向けの“モダンな”AGENTS.mdの典型パターンです。[^4][^5][^3]

***

## フォルダ専用：`services/payments` 向けテンプレ

決済モジュールだけルールをカスタムしたいときの、サブディレクトリ用 AGENTS.override.md / AGENTS.md の書き方例です。[^7][^5]

```markdown
# AGENTS for `services/payments/`

## Scope

These instructions apply only to code under `services/payments/`.
They override more general project-level guidance when in conflict.

## Domain Rules

- This folder handles payments, refunds, and billing-related logic only.
- Never log full card numbers or sensitive PII.
- All operations must be idempotent when retried.

## Code Structure

- Use a clear command / handler pattern for payment flows.
- Keep external API client code in `{例: services/payments/gateways/**}`.
- Validation and normalization should happen before calling external gateways.

## Testing

- Every new flow must include:
  - happy-path tests,
  - failure-case tests (gateway errors, timeouts),
  - idempotency tests when relevant.

## AI Agent Guidelines

- Do not change gateway API contracts unless explicitly asked.
- When touching money-related logic, favor explicitness over cleverness.
- Before major changes, output a short checklist of cases you will cover
  (success, network failure, partial failure, retry, etc.).
```

最近の解説系ブログでも、「ルートAGENTS.mdには一般ルールを、特定フォルダにはドメイン固有ルールだけを短く書く」という分離が推奨されています。[^8][^5]

***

## グローバル `~/.codex/AGENTS.md` のモダン例

ここは「自分個人の好み」と「AIにやってほしい共通ムーブ」だけに絞るのが、実用的かつ今風です。[^7][^3][^4]

```markdown
# Global AGENTS for My Coding

## Personal Preferences

- Prefer concise, well-structured code over clever one-liners.
- Write descriptive names for functions, variables, and classes.
- Keep comments short and focused on "why", not "what".

## General Expectations

- When making changes, propose relevant tests and documentation updates.
- Use existing tools in the repo (formatter, linter, test runner) instead of inventing new ones.
- If there is an established pattern in the codebase, follow it.

## Collaboration Style

- Explain non-trivial changes in a short summary before editing many files.
- When in doubt, ask clarifying questions instead of making risky assumptions.
- Avoid large rewrites unless explicitly requested.
```

OpenAI自身も「グローバルAGENTSはすべてのプロジェクトに共通する好み・スタイルに限定し、重いプロジェクト固有情報はプロジェクト側に書け」とガイドしています。[^5][^3][^7]

***

## どうカスタムしていくと良いか

- まずは上の **プロジェクト用テンプレをルート `AGENTS.md` に** 置き、
    - 実際の tech stack
    - よく使うコマンド
    - そのプロジェクト特有の「やらかしポイント」
だけ埋めて運用を始めるのが、最近のガイドでも推奨されるスタートラインです。[^2][^3][^5]
- その後、**頻繁に触るドメイン（payments / auth / OCR など）にだけフォルダ用AGENTS** を追加していくと、肥大化せずに“効きのよい”構成にできます。[^8][^5]

もし「今のRails(or Next)プロジェクトのディレクトリ構成」をざっと貼ってもらえれば、その構成にピッタリ合わせた AGENTS.md（ルート＋サブフォルダ）を具体的な文面まで一緒に起こします。
<span style="display:none">[^10][^11][^12][^13][^14][^15][^9]</span>

<div align="center">⁂</div>

[^1]: https://agentsmd.io

[^2]: https://agents.md

[^3]: https://agentsmd.io/how-to-use-agents-md-in-codex

[^4]: https://agentsmd.net

[^5]: https://www.vibecoding.com/2025/06/05/how-to-configure-agents-md-files-to-supercharge-your-codex-ai-agent-performance/

[^6]: https://www.theunwindai.com/p/agents-md-for-openai-codex-cursor-google-jules

[^7]: https://developers.openai.com/codex/guides/agents-md

[^8]: https://nextat.co.jp/staff/archives/390

[^9]: https://developers.openai.com/codex/guides/agents-md/

[^10]: https://openai.com/index/introducing-codex/

[^11]: https://qiita.com/oga_aiichiro/items/2a3aeb6262b49ae55c46

[^12]: https://github.com/danielrosehill/Agents.md-Templates

[^13]: https://www.youtube.com/watch?v=A1wct93Haz4

[^14]: https://www.agentsmd.net/agents-md-examples/

[^15]: https://zenn.dev/tkithrta/articles/898bf6c84f8584

