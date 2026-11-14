# shoulder# 肩たたき券

家族で共有する肩たたき券のPWAスターターです。Next.js + Supabaseを使用します。

## 現在実装しているもの

- メールアドレス・パスワード認証
- 管理者 / 一般ユーザーの権限表示
- チケット残数表示
- 確認・キャンセル付きの使用操作
- 使用・追加・取り消し・リセットの履歴表示
- 管理者だけの券追加、初期枚数設定、残数リセット、使用取り消し
- Supabase RPCによる残数変更の原子的処理
- スマホ向けレイアウトとPWA manifest

## 必要なもの

- Node.js 20.9以上
- Supabaseプロジェクト
- Vercelアカウント（公開する場合）

## セットアップ

1. Supabaseで新しいプロジェクトを作成します。
2. SupabaseのSQL Editorで `supabase/schema.sql` の内容を実行します。
3. このプロジェクトをローカルに展開します。
4. `.env.example` を `.env.local` にコピーし、SupabaseのProject URLとanon/public keyを設定します。
5. ターミナルで以下を実行します。

```bash
npm install
npm run dev
```

6. `http://localhost:3000` を開き、最初のアカウントを登録します。
7. SupabaseのAuthentication > Usersで自分のユーザーUUIDを確認します。
8. SQL Editorで以下を実行します（UUIDを置き換える）。

```sql
update public.profiles
set role = 'admin'
where id = 'YOUR-USER-UUID';
```

9. 母のメールアドレスで別アカウントを登録します。母は一般ユーザーとして登録されます。
10. SupabaseのAuthentication > Usersで母のユーザーUUIDを確認し、SQL Editorで `update public.profiles set is_active = true where id = 'MOTHERS-USER-UUID';` を実行します。
11. 管理者として再ログインし、管理画面の初期枚数設定で「券を受け取る人」に母を選択して枚数を設定します。

## 公開

GitHubにプロジェクトをpushし、VercelでImportします。VercelのEnvironment Variablesに `.env.local` と同じ2つの値を登録し、Deployします。HTTPSの公開URLから利用できます。

## 大切な注意

- Supabaseの `service_role` キーはブラウザ・GitHub・Vercelの公開環境変数に絶対に置かないでください。
- このスターターは初期構築用です。実運用前に、Supabaseのメール確認設定、パスワードリセット導線、バックアップ、管理者の多要素認証を確認してください。
- 管理者権限の付与はSupabase SQL Editorから行います。一般ユーザーの画面から自分のroleを変更できません。
- 現状のアカウント作成はメールアドレスを使います。メールアドレスを持たない利用者向けのユーザー名ログインは別途実装が必要です。
- 使用取り消しは元の使用分を戻す方式です。残数の変更後も元履歴と取り消し履歴の両方が残ります。
- 初期枚数の設定は初回のみです。以後の残数変更には管理者リセット機能を使います。
- 現在のUIは1つの共有チケット口座（母が受け取る券）を表示します。家族個別の券残高を管理する設計ではありません。
