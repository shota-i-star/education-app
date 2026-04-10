# AZism 教育進捗管理 — プロジェクト仕様書

> この仕様書は、AIとの会話が長くなっても開発の方向性を維持するための「共通の地図」です。
> 新しい会話を始めるときは、この仕様書を貼り付けてから質問してください。

---

## 1. プロジェクト概要

| 項目 | 内容 |
|------|------|
| アプリ名 | AZism 教育進捗管理 |
| 種類 | Webアプリ（ブラウザで動作）・PWA対応（ホーム画面追加可） |
| 対象ユーザー | 全社員（MG・メンバー・管理者） |
| 開発者のスキル | プログラミング未経験。作りながら学ぶスタイルで進める |
| 公開方法 | GitHub Pages（静的ホスティング） |

---

## 2. 解決したい課題

- 誰がどの教育項目をどこまで終えたか、一目でわからない
- テストの点数や進捗を手作業（Excel等）で管理しており、集計が煩雑
- 上司とメンバーで見せる情報を分けたい

---

## 3. 主な機能要件

### メンバー向け画面
- チェックリスト形式で教育項目を完了マークできる
- テストを受けて点数が記録される（テストが設定されている等級のみボタン表示）
- 過去の等級のコンテンツを閲覧できる
- チェック時に画面が白くフラッシュしない（silent再描画）

### MG・管理者向け画面
- 全メンバーの進捗を一覧・集計して見られる
- 進捗率は「全体進捗率」「現等級進捗率」の2軸で表示
- サマリーカードに「メンバー数」「平均進捗率（全体）」「平均進捗率（現等級）」「テスト平均点」「未合格者数」の5指標を表示
- 進捗率セクションは「エリア別」「店舗別」「等級別」「個人別」の4タブ
- 等級別タブ：0〜3等級ごとにメンバーをグループ化し、展開でメンバー一覧表示
- エリア別・店舗別タブで行をクリックするとその配下のメンバー一覧が展開表示される
- テストの平均点・個人別スコアを確認できる
- 進捗率をエリア別・店舗別グラフで把握できる
- メンバー詳細画面でチェック状況・テスト履歴・コメントを確認できる
- メンバー詳細画面からPDFで出力できる

### 管理者向け画面
- メンバー管理（追加・部署変更・等級変更・役割変更・削除）
- メンバー編集モーダル：名前・社員番号・パスワードを1画面で変更
- メンバー追加時に部署の設定が必須
- 社員番号変更・パスワードリセット（全権管理者のみ）
- メンバーデータのCSVエクスポート（名前・社員番号・部署・等級・店舗・エリア・進捗率・テスト点数・合否）
- CSV一括インポート：メンバー・カテゴリ・店舗・項目（テンプレートCSVあり）
- 部署・エリア・店舗管理（追加・削除・トグル表示・店舗のエリア間移動）
- 事業部名・エリア名の編集（全権管理者のみ）
- カテゴリ管理（追加・カテゴリ名編集・複製・削除・CSV一括追加）
- 項目管理（追加・No.表示・項目名編集・複製・削除・CSV一括追加）
- テスト管理（部署×等級ごとに整理・トグル表示）
- 記述式テストの採点
- 再試験管理
- カテゴリ複製機能（manager_admin のみ）

### 共通機能
- ログイン機能（役割に応じて画面を切り替え）
- ログアウト機能（全画面共通）
- データはSupabaseに保存（リロードしても消えない）
- スマートフォン対応（member_top.html・test_page.html）
- PWA対応：Safari の「ホーム画面に追加」でアプリとしてインストール可能
- AZ-GROUPロゴを全ページヘッダーに表示

---

## 4. 開発フェーズ（ロードマップ）

| フェーズ | テーマ | 状態 |
|---------|--------|------|
| **1** | 画面の骨格を作る | ✅ 完了 |
| **2** | データを動かす | ✅ 完了 |
| **3** | ログインで役割を分ける | ✅ 完了 |
| **4** | 集計・レポートを作る | ✅ 完了 |
| **5** | 仕上げ・公開準備 | ✅ 完了 |
| **6** | GitHub Pages で公開・運用改善 | 🔄 進行中 |

---

## 5. 技術スタック

| 役割 | 技術 | 補足 |
|------|------|------|
| フロントエンド | HTML / CSS / JavaScript | |
| バックエンド・DB | Supabase | 導入済み |
| 認証 | Supabase Auth | 全画面対応済み |
| Supabase SDKバージョン | `@2.39.3`（安定版固定） | |
| 開発環境 | VSCode + Live Server拡張 | 導入済み |
| 公開 | GitHub Pages | 公開済み |
| AIコーディング | Cowork（Claude）| ファイル直接編集対応 |
| PWA | manifest.json + メタタグ | ホーム画面追加対応 |

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
| role | text | member / manager / manager_admin / dept_admin |
| grade | int | 等級（0〜5） |
| store | text | 所属店舗 |
| area | text | 所属エリア |
| department_id | int8 | 所属部署ID（全ロール・メンバー追加時に必須） |

**注意：** `department_id` は全ロールで使用。メンバー追加モーダルで必須入力。

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

**表示順：** `id` 昇順。カテゴリ内でのNo.は `id` 順の連番で自動付与。

### check_steps（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| item_id | int8 | check_itemsのID |
| name | text | ステップ名（例：聞いた・やってみた） |
| order_no | int | 順番（前のステップが完了しないと次は押せない） |

### 進捗率の計算方法（重要・v16更新）

**現等級進捗率**
```
現等級進捗率 = チェック済みstep数（現在の等級のみ）÷ 現在等級の全step数 × 100
```

**全体進捗率**
```
全体進捗率 = チェック済みstep数 ÷ 部署の全カテゴリ・全等級のstep数 × 100
```

- ダッシュボードでは両方を2軸バー（`dualProgressCell`）で表示
- サマリーカードにも両方の平均を表示
- 分母の特定には `category_departments` テーブル（カテゴリ×部署の中間テーブル）が必要

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
| name | text | カテゴリ名（ユニーク制約なし・部署ごとに独立） |
| department_id | int8 | 旧カラム（INSERT時に department_id を渡すことで NOT NULL 制約を回避） |

**重要：** カテゴリ追加時は `{ name, department_id }` を両方 INSERT すること。実際の部署紐づけは `category_departments` で管理する。

### category_departments（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID（categories.id） |
| department_id | int8 | 部署ID（departments.id） |

**ユニーク制約：** `category_id` + `department_id`
**ON DELETE CASCADE：** カテゴリ削除時に自動で紐づけも削除される
**用途：** 全体進捗率の計算にも使用（部署ごとの対象ステップを特定するため）

### test_sets（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID |
| grade | int | 対象等級 |
| name | text | 問題集名 |
| pass_score | int | 合格基準点 |

**注意（v16〜）：** `member_top.html` はこのテーブルをクエリし、等級に対応するテストがない場合は「テストを受ける」ボタンを表示しない。

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

### test_choices（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| question_id | int8 | 問題ID（test_questions.id） |
| body | text | 選択肢の内容 |
| is_correct | boolean | 正解かどうか |

**注意：** `test_questions` を削除する前に `test_choices` を先に削除すること（FK制約）。

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
| ログイン画面 | login.html | ✅ 完成（PWA対応・ロゴ追加） |
| メンバー：トップ | member_top.html | ✅ 完成（テストボタン制御・白フラッシュ解消・スマホ対応・PWA対応） |
| メンバー：テスト | test_page.html | ✅ 完成（スマホ対応・PWA対応） |
| MG：ダッシュボード | manager_dashboard.html | ✅ 完成（2軸進捗・等級別タブ・全体進捗サマリー・PWA対応） |
| MG：メンバー詳細 | member_detail.html | ✅ 完成（コメント・PDF出力・PWA対応） |
| 管理者：管理画面 | admin.html | ✅ 完成（全管理機能・CSV一括追加・編集UI統合・PWA対応） |

---

## 8. ログイン・権限管理

### 役割と画面の対応
| role（DB値） | 表示名 | ログイン後の遷移先 | 権限範囲 |
|------|--------|-----------------|---------|
| member | メンバー | member_top.html | 自分のみ |
| manager | MG | manager_dashboard.html | 全部署ダッシュボード閲覧 |
| manager_admin | 全権管理者 | admin.html | 全部署閲覧＋全管理機能＋社員番号変更・PW変更・名称編集 |
| dept_admin | 部署管理者 | manager_dashboard.html | 自部署のみ閲覧・管理 |

**注意：** `admin` ロールは廃止済み。

### ロール付与の制限
- `manager_admin` ロールは**全権管理者のみ**が付与可能
- `dept_admin` は manager_admin を付与できない（選択肢自体が非表示）

### ログインの仕組み
- 社員番号 → `社員番号@education-app.com` のメールアドレスに変換
- 新しいユーザー追加時は `adminClient.auth.admin.createUser()` で Supabase Auth に登録

---

## 9. 組織階層

```
部署（departments）
　└ エリア（areas）
　　　└ 店舗（stores）
```

- 部署・エリア・店舗は admin.html から追加・削除できる
- 店舗は「移動」ボタンで他エリアに移動できる（組織再編対応）
- 事業部名・エリア名は全権管理者のみ「編集」ボタンで変更可能

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
| manager_dashboard.html | ダッシュボード | 自部署の店舗に属するメンバーのみ（role=member）表示 |
| manager_dashboard.html | 昇級申請 | 自部署メンバーの申請のみ表示 |
| admin.html | メンバー管理 | 自部署の member/manager/dept_admin を department_id で直接フィルタ |
| admin.html | 記述式採点 | 自部署メンバーの回答のみ表示 |
| admin.html | 再試験管理 | 自部署メンバーのみ表示 |
| admin.html | 部署・エリア・店舗管理 | 自部署のみ表示。部署の追加・削除・編集ボタンは非表示 |
| admin.html | カテゴリ・項目管理 | 自部署に紐づくカテゴリのみ表示 |
| admin.html | テスト管理 | 自部署に紐づくカテゴリの問題集のみ表示 |

### ダッシュボードと管理画面のフィルター差異（重要）
| 画面 | フィルター方式 | 対象ロール |
|------|-------------|---------|
| ダッシュボード（manager_dashboard） | 店舗名→エリア→部署の階層で絞り込み | member のみ |
| 管理画面メンバー一覧（admin） | `department_id` で直接フィルタ | member / manager / dept_admin |

---

## 12. カテゴリ管理の仕様

### 部署ごとの独立管理
- カテゴリは**部署ごとに完全に独立**したレコードとして管理する
- `categories.name` のユニーク制約は解除済み
- カテゴリ追加時は `{ name, department_id }` を INSERT し、`category_departments` にも紐づけを追加する

### カテゴリ表示構造（v15〜）
以前：部署 → 等級 → カテゴリ（項目のない空カテゴリが表示されない問題あり）
現在：**部署 → カテゴリ → 等級別項目**（項目ゼロでもカテゴリカードが常に表示される）

### カテゴリ複製（manager_admin のみ）
- カテゴリカードのヘッダーに「複製」ボタンが表示される
- 複製先の部署を選択すると、カテゴリ・全項目・全ステップをまるごとコピーする

---

## 13. UIの仕様

### ダッシュボード「進捗率」セクション（v16〜）
- セクション名：「メンバー一覧」→「**進捗率**」に変更
- タブ構成：**エリア別 / 店舗別 / 等級別（新規） / 個人別**
- **等級別タブ**：0〜3等級ごとにまとめ、タップで配下メンバー展開。各行に2軸進捗バー表示

### ダッシュボードのサマリーカード（v16〜）
5列グリッド構成：
1. メンバー数
2. 平均進捗率（全体）← 新規追加
3. 平均進捗率（現等級）
4. テスト平均点
5. 未合格者数

### 2軸進捗バー（v16〜）
- 「全体」バー（グレー）と「現等級」バー（ブルー）を縦並びで表示
- `dualProgressCell(progressAll, progressGrade)` 関数で統一生成
- 個人別・エリア別・店舗別・等級別すべてのテーブルで使用

### トグルタブ（admin.html）
- 部署カード・等級セクション・カテゴリカードのヘッダーをクリックすると開閉する
- データ追加・編集・削除後の再描画でも開閉状態を維持する（`closedIds` 方式）

### メンバー編集モーダル（admin.html・v16〜）
- ボタンを「編集」「削除」の2つに統合
- 「編集」ボタンから1つのモーダルで名前・社員番号・パスワードを変更
- 社員番号・PW変更フィールドは全権管理者のみ表示（`currentRole === 'manager_admin'` で制御）

### メンバー一覧テーブル（admin.html）
- 列順：名前 | 社員番号 | 部署（幅17%） | 店舗 | 等級 | 役割 | 操作（幅23%）
- PCでは横スクロールなし（`table-layout: fixed`）
- 部署列に `overflow: hidden` + `box-sizing: border-box` で選択UIのはみ出しを防止

### CSV一括インポート（v16〜）
- 共通CSVモーダルで「ファイル選択→プレビュー確認→追加」の3ステップ
- 文字コード自動判別（UTF-8 BOM / UTF-16 / Shift-JIS）
- ロール値の表記揺れを自動正規化（`toLowerCase().replace(/\s+/g,'_')`）
- テンプレートファイル：`csv_template_members.csv` / `csv_template_categories.csv` / `csv_template_stores.csv` / `csv_template_items.csv`

### PWA設定（v16〜）
- `manifest.json` を全ページで読み込み
- Safari（iOS）で「ホーム画面に追加」でアプリとしてインストール可能
- 起動時はアドレスバーなしのフルスクリーン表示（`display: standalone`）
- 起動 URL：`member_top.html`（メンバー向け）

### ヘッダーロゴ（v16〜）
- 全ページヘッダー左側に AZ-GROUP ロゴを表示
- ヘッダー（紺背景）：`logo-white.png` + `filter: brightness(0) invert(1)` で白く表示
- ログインページ（白背景）：`icon-192.png`（カラー）をそのまま表示

### CSVエクスポート仕様
- 列順：名前, 社員番号, 部署, 等級, 店舗, エリア, 進捗率(%), テスト点数, 合否
- BOM付き（`\uFEFF`）でExcel日本語対応
- dept_adminの場合は自部署メンバーのみ出力

### メンバー詳細のPDF出力
- 「📄 PDFで出力」ボタン（パンくずの右端）をクリック
- ブラウザの印刷ダイアログ → 「PDFとして保存」でPDF化

---

## 14. 社員番号変更・パスワードリセット（全権管理者のみ）

```javascript
// 社員番号変更：user_profiles と auth email を両方更新
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });

// パスワードリセット
await adminClient.auth.admin.updateUserById(userId, { password: newPw });
```

---

## 15. データ削除順序（重要）

```sql
DELETE FROM text_answers;
DELETE FROM test_results;
DELETE FROM check_progress;
DELETE FROM comments;
DELETE FROM retest_permissions;
DELETE FROM grade_up_requests;
DELETE FROM test_choices;
DELETE FROM test_questions;
DELETE FROM test_sets;
DELETE FROM check_steps;
DELETE FROM check_items;
DELETE FROM category_departments;
DELETE FROM categories;
DELETE FROM stores;
DELETE FROM areas;
DELETE FROM user_profiles WHERE name NOT IN ('残すユーザー名');
```

---

## 16. AIへの進め方の指示

1. **フェーズを守る** — 現在のフェーズ外の実装は提案しない
2. **コードには必ず説明をつける** — 日本語で解説する
3. **専門用語は都度説明する** — 具体例を使って説明する
4. **小さく確認しながら進める** — 小さな単位で動作確認してから次へ
5. **ユーザーに選択肢を示す** — 複数の方法がある場合はメリット・デメリットを説明する
6. **わからない点は聞き返す** — 要件が曖昧な場合は実装前に確認する

---

## 17. 変更履歴

| 日付 | 内容 |
|------|------|
| 2026-04-07 | 初版作成・フェーズ1完了 |
| 2026-04-08 | フェーズ2〜5完了。全管理機能・コメント・昇級申請・部署管理・dept_adminフィルター |
| 2026-04-09 | Cowork移行・GitHub Pages 公開・各種UI改善（v13） |
| 2026-04-09 | アプリ名「AZism 教育進捗管理」・MG表記・adminロール廃止・社員番号/PW変更機能（v14） |
| 2026-04-09 | メンバー一覧PC横スクロール修正・部署必須化・管理画面ボタンデザイン変更 |
| 2026-04-09 | dept_adminフィルターを department_id 直接参照に変更 |
| 2026-04-09 | カテゴリ追加バグ修正・カテゴリ管理の表示構造変更・項目編集/複製/No.追加 |
| 2026-04-09 | メンバー名前編集・カテゴリ名編集・トグル状態保持・事業部/エリア名編集（v15） |
| 2026-04-10 | メンバー編集UI統合（2ボタン/1モーダル）・CSV一括追加（メンバー/カテゴリ/店舗/項目） |
| 2026-04-10 | Shift-JIS文字コード対応・ロール正規化・ボタン位置移動 |
| 2026-04-10 | 2軸進捗率（全体/現等級）・サマリーカード拡張・等級別タブ追加・進捗率セクション名変更 |
| 2026-04-10 | 部署列幅調整・テストボタン表示制御・チェック白フラッシュ解消 |
| 2026-04-10 | PWA対応・AZ-GROUPロゴ全ページ設置・ファビコン設定（v16） |
