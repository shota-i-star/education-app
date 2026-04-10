# 飲食事業部 教育管理アプリ — プロジェクト仕様書

> この仕様書は、AIとの会話が長くなっても開発の方向性を維持するための「共通の地図」です。
> 新しい会話を始めるときは、この仕様書を貼り付けてから質問してください。

---

## 1. プロジェクト概要

| 項目 | 内容 |
|------|------|
| アプリ名 | 飲食事業部 教育管理アプリ |
| 種類 | Webアプリ（ブラウザで動作） |
| 対象ユーザー | 飲食事業部（上司・メンバー） |
| 開発者のスキル | プログラミング未経験。作りながら学ぶスタイルで進める |

---

## 2. 解決したい課題

- 誰がどの教育項目をどこまで終えたか、一目でわからない
- テストの点数や進捗を手作業（Excel等）で管理しており、集計が煩雑
- 上司とメンバーで見せる情報を分けたい

---

## 3. 主な機能要件

### メンバー向け画面
- チェックリスト形式で教育項目を完了マークできる
- テストを受けて点数が記録される
- 過去の等級のコンテンツを閲覧できる

### 上司向け画面
- 全メンバーの進捗を一覧・集計して見られる
- テストの平均点・個人別スコアを確認できる
- 進捗率をエリア別・店舗別グラフで把握できる
- メンバー詳細画面でチェック状況・テスト履歴・コメントを確認できる

### 管理者向け画面
- メンバー管理（追加・等級変更・役割変更・削除）
- 部署・エリア・店舗管理（追加・削除）
- カテゴリ・項目管理（部署×等級ごとに整理）
- テスト管理（部署×等級ごとに整理）
- 記述式テストの採点
- 再試験管理

### 共通機能
- ログイン機能（役割に応じて画面を切り替え）
- ログアウト機能（全画面共通）
- データはSupabaseに保存（リロードしても消えない）

---

## 4. 開発フェーズ（ロードマップ）

| フェーズ | テーマ | 状態 |
|---------|--------|------|
| **1** | 画面の骨格を作る | ✅ 完了 |
| **2** | データを動かす | ✅ 完了 |
| **3** | ログインで役割を分ける | ✅ 完了 |
| **4** | 集計・レポートを作る | ✅ 完了 |
| **5** | 仕上げ・公開 | ✅ 完了 |

---

## 5. 技術スタック

| 役割 | 技術 | 補足 |
|------|------|------|
| フロントエンド | HTML / CSS / JavaScript | |
| バックエンド・DB | Supabase | 導入済み |
| 認証 | Supabase Auth | 全画面対応済み |
| Supabase SDKバージョン | `@2.39.3`（安定版固定） | |
| 開発環境 | VSCode + Live Server拡張 | 導入済み |
| AIコーディング | Cowork（Claude）| ファイル直接編集対応 |

### Supabase接続情報
| 項目 | 値 |
|------|------|
| Project URL | `https://rkclfgpewmjdluwufjng.supabase.co` |
| anon key | コード内の `SUPABASE_ANON_KEY` 変数に記載 |
| service role key | コード内の `SUPABASE_SERVICE_KEY` 変数に記載（admin.htmlのみ） |

### Supabase初期化の書き方
```javascript
// 通常画面（persistSession: true）
var client = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY, {
  auth: { persistSession: true }
});

// 管理者画面のみ（adminClient）
var adminClient = supabase.createClient(SUPABASE_URL, SUPABASE_SERVICE_KEY, {
  auth: { persistSession: false }
});
```

---

## 6. データベース設計

### user_profiles（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | uuid | Supabase AuthのユーザーIDと紐づく |
| employee_id | text | 社員番号 |
| name | text | 表示名 |
| role | text | member / boss / admin / boss_admin / dept_admin |
| grade | int | 等級（0〜5） |
| store | text | 所属店舗 |
| area | text | 所属エリア |
| department_id | int8 | 所属部署ID（dept_adminのみ使用） |

### check_progress（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| user_id | uuid | ユーザーID |
| item_id | text | チェック項目ID（旧・現在はstep_idを使用） |
| checked | bool | チェック状態 |
| step_id | int8 | ステップID（フェーズ5から追加） |

**ユニーク制約：** `user_id` + `item_id`
**注意：** 現在は `step_id` を使ってチェック状況を管理。`item_id` は旧形式。

### check_items（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID |
| name | text | 項目名 |
| grade | int | 対象等級 |

### check_steps（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| item_id | int8 | check_itemsのID |
| name | text | ステップ名（例：聞いた・やってみた） |
| order_no | int | 順番（前のステップが完了しないと次は押せない） |

### 進捗率の計算方法（重要）
```
進捗率 = チェック済みstep数 ÷ 全step数 × 100
```
- `check_progress` の `step_id` が NULL でないものをカウントする
- 分母は現在の等級の項目に紐づく `check_steps` のレコード数
- **旧方式（item_idベース）は使わない**

### test_results（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| user_id | uuid | ユーザーID |
| category | text | カテゴリ名 |
| grade | int | 等級 |
| score | int | 4択・○×の点数 |
| passed | boolean | 合否 |
| q1_answer | int | 問1の回答 |
| q2_answer | text | 問2の回答 |
| taken_at | timestamp | 受験日時 |

### text_answers（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| user_id | uuid | ユーザーID |
| category | text | カテゴリ名 |
| grade | int | 等級 |
| question | text | 問題文 |
| answer | text | 回答 |
| score | int | 管理者採点（初期NULL） |
| graded_at | timestamp | 採点日時 |
| submitted_at | timestamp | 提出日時 |

### comments（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| member_id | uuid | コメント対象メンバーID |
| author_id | uuid | コメントした上司のID |
| author_name | text | 上司の名前 |
| body | text | コメント内容 |
| created_at | timestamp | 投稿日時 |

### departments（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| name | text | 部署名 |

### areas（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| name | text | エリア名 |
| department_id | int8 | 所属部署ID |

### stores（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| name | text | 店舗名 |
| area_id | int8 | エリアID |

### categories（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| name | text | カテゴリ名（ユニーク制約あり） |
| department_id | int8 | 旧カラム（現在は category_departments を使用） |

### category_departments（作成済み）★新規
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID（categories.id） |
| department_id | int8 | 部署ID（departments.id） |

**ユニーク制約：** `category_id` + `department_id`
**ON DELETE CASCADE：** カテゴリ削除時に自動で紐づけも削除される
**用途：** 1つのカテゴリを複数部署に紐づけるための中間テーブル

### categories とのデータ引き継ぎ
```sql
-- categories.department_id のデータを中間テーブルに移行済み
INSERT INTO category_departments (category_id, department_id)
SELECT id, department_id FROM categories WHERE department_id IS NOT NULL;
```

### test_sets（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID |
| grade | int | 対象等級 |
| name | text | 問題集名 |
| pass_score | int | 合格基準点 |

### test_questions（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| test_set_id | int8 | 問題集ID |
| category_id | int8 | カテゴリID |
| grade | int | 等級 |
| question | text | 問題文 |
| type | text | choice / truefalse / text |
| point | int | 配点 |
| pass_score | int | 合格基準点（test_setsで管理） |

### test_choices（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| question_id | int8 | 問題ID |
| body | text | 選択肢の内容 |
| is_correct | boolean | 正解かどうか |

### retest_permissions（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| user_id | uuid | ユーザーID |
| category | text | カテゴリ名 |
| grade | int | 等級 |
| permitted_at | timestamp | 許可日時 |
| used_at | timestamp | 使用日時（NULLは未使用） |

### grade_up_requests（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| member_id | uuid | メンバーのuuid |
| member_name | text | メンバー名 |
| current_grade | int | 申請時の等級 |
| status | text | pending / approved / rejected |
| requested_at | timestamp | 申請日時 |
| resolved_at | timestamp | 承認・却下日時 |

---

## 7. 画面一覧・状態

| 画面 | ファイル名 | 状態 |
|------|-----------|------|
| ログイン画面 | login.html | ✅ 完成 |
| メンバー：トップ | member_top.html | ✅ 完成 |
| メンバー：テスト | test_page.html | ✅ 完成 |
| 上司：ダッシュボード | boss_dashboard.html | ✅ 完成 |
| 上司：メンバー詳細 | member_detail.html | ✅ 完成 |
| 管理者：管理画面 | admin.html | ✅ 完成 |

---

## 8. ログイン・権限管理

### 役割と画面の対応
| role | 表示名 | ログイン後の遷移先 | 権限範囲 |
|------|--------|-----------------|---------|
| member | メンバー | member_top.html | 自分のみ |
| boss | 上司 | boss_dashboard.html | 全部署 |
| admin | 管理者 | admin.html | 全管理機能 |
| boss_admin | 全権管理者 | boss_dashboard.html | 全部署＋全管理機能 |
| dept_admin | 部署管理者 | boss_dashboard.html | 自部署のみ閲覧・管理 |

### 登録済みユーザー
| 名前 | 社員番号 | パスワード | 役割 | 店舗 | エリア |
|------|---------|-----------|------|------|------|
| 山田 太郎 | 10001 | yamada1234 | member | 渋谷店 | 東京エリア |
| 佐藤 花子 | 10002 | sato1234 | member | 渋谷店 | 東京エリア |
| 田中 健二 | 10003 | tanaka1234 | member | 新宿店 | 東京エリア |
| 木村 あかり | 10004 | kimura1234 | member | 新宿店 | 東京エリア |
| 中村 誠 | 10005 | nakamura1234 | member | 梅田店 | 大阪エリア |
| 小林 めぐみ | 10006 | kobayashi1234 | member | 難波店 | 大阪エリア |
| 鈴木 部長 | 00001 | suzuki1234 | boss_admin | － | 東京エリア |

### ログインの仕組み
- 社員番号 → `社員番号@education-app.com` のメールアドレスに変換
- `login.html` 内の `emailMap` で例外的なアドレスを管理
- 新しいユーザー追加時は Supabase Auth にメールアドレス形式で登録される

---

## 9. 組織階層

```
部署（飲食事業部・エンタメ事業部・営業サポート部・本社・指令室本部）
　└ エリア（東京エリア・大阪エリア・本社）
　　　└ 店舗（渋谷店・新宿店・梅田店・難波店 等）
```

---

## 10. デザインカラー

| 役割 | カラーコード |
|------|------------|
| ブランドブルー | `#495e9f` |
| ブランドレッド | `#bd3c40` |
| 背景 | `#f0f4f8` |
| カード | `#ffffff` |
| テキスト（メイン） | `#1a1a2e` |
| テキスト（サブ） | `#6b7280` |

---

## 11. dept_admin（部署管理者）の仕様

### フィルター実装済み画面
| 画面 | 対象 | 実装内容 |
|------|------|---------|
| boss_dashboard.html | ダッシュボード | 自部署の店舗に属するメンバーのみ表示 |
| boss_dashboard.html | 昇級申請 | 自部署メンバーの申請のみ表示 |
| admin.html | メンバー管理 | 自部署メンバーのみ表示 |
| admin.html | 記述式採点 | 自部署メンバーの回答のみ表示 |
| admin.html | 再試験管理 | 自部署メンバーのみ表示 |
| admin.html | 部署・エリア・店舗管理 | 自部署のみ表示。部署の追加・削除ボタンは非表示 |
| admin.html | カテゴリ・項目管理 | 自部署に紐づくカテゴリのみ表示 |
| admin.html | テスト管理 | 自部署に紐づくカテゴリの問題集のみ表示 |

### フィルターのロジック（boss_dashboard / admin 共通）
```javascript
// グローバル変数で保持
var currentRole      = null;  // ログインユーザーの役割
var currentDeptId    = null;  // dept_admin の department_id
var allowedMemberIds = null;  // null=全員 / 配列=自部署メンバーのみ

// 絞り込みの流れ（initDeptFilter）
// 1. areas テーブルから department_id が一致するエリアIDを取得
// 2. stores テーブルからそのエリアに属する店舗名を取得
// 3. user_profiles.store がその店舗名に含まれるメンバーIDのみ保持
```

### カテゴリの部署紐づけ（category_departments）
- 1つのカテゴリを複数の部署に紐づけられる（チェックボックスで選択）
- 全部署共通のカテゴリは全部署にチェックを入れて登録する
- dept_admin がカテゴリを追加すると自部署に自動紐づけされる
- カテゴリ名が重複する場合は新規作成せず既存カテゴリに部署を追加する

---

## 12. AIへの進め方の指示

1. **フェーズを守る** — 現在のフェーズ外の実装は提案しない
2. **コードには必ず説明をつける** — 日本語で解説する
3. **専門用語は都度説明する** — 具体例を使って説明する
4. **小さく確認しながら進める** — 小さな単位で動作確認してから次へ
5. **ユーザーに選択肢を示す** — 複数の方法がある場合はメリット・デメリットを説明する
6. **わからない点は聞き返す** — 要件が曖昧な場合は実装前に確認する

---

## 13. 変更履歴

| 日付 | 内容 |
|------|------|
| 2026-04-07 | 初版作成・フェーズ1完了 |
| 2026-04-08 | フェーズ2〜4完了 |
| 2026-04-08 | フェーズ5開始。管理画面の全機能実装・コメント機能実装 |
| 2026-04-08 | 昇級申請機能（grade_up_requests）実装完了 |
| 2026-04-08 | showToast修正：引数ありに統一・申請済み時メッセージ追加 |
| 2026-04-08 | 部署管理機能追加（departments テーブル・3階層表示） |
| 2026-04-08 | 役割に dept_admin（部署管理者）・boss_admin を全権管理者に変更 |
| 2026-04-08 | user_profiles に department_id カラム追加 |
| 2026-04-08 | メンバー追加モーダルに部署選択欄追加（部署管理者のみ表示） |
| 2026-04-09 | Cowork（Claude）に開発環境を移行 |
| 2026-04-09 | dept_admin フィルター実装（boss_dashboard・admin 両画面） |
| 2026-04-09 | login.html に dept_admin のログイン後遷移先を追加 |
| 2026-04-09 | admin.html：部署・エリア・店舗管理に dept_admin フィルター追加 |
| 2026-04-09 | categories テーブルに department_id カラム追加 |
| 2026-04-09 | category_departments 中間テーブルを作成（多対多対応） |
| 2026-04-09 | カテゴリ・項目管理を「部署→等級→カテゴリ→項目」構造に変更 |
| 2026-04-09 | テスト管理を「部署→等級→問題集→問題」構造に変更 |
| 2026-04-09 | カテゴリ追加モーダルをチェックボックス式（複数部署選択可）に変更 |
