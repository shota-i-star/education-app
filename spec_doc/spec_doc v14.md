# AZism 教育進捗管理 — プロジェクト仕様書

> この仕様書は、AIとの会話が長くなっても開発の方向性を維持するための「共通の地図」です。
> 新しい会話を始めるときは、この仕様書を貼り付けてから質問してください。

---

## 1. プロジェクト概要

| 項目 | 内容 |
|------|------|
| アプリ名 | AZism 教育進捗管理 |
| 種類 | Webアプリ（ブラウザで動作） |
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
- テストを受けて点数が記録される
- 過去の等級のコンテンツを閲覧できる

### MG・管理者向け画面
- 全メンバーの進捗を一覧・集計して見られる
- テストの平均点・個人別スコアを確認できる
- 進捗率をエリア別・店舗別グラフで把握できる
- エリア別・店舗別タブで行をクリックするとその配下のメンバー一覧が展開表示される
- メンバー詳細画面でチェック状況・テスト履歴・コメントを確認できる
- メンバー詳細画面からPDFで出力できる

### 管理者向け画面
- メンバー管理（追加・部署変更・等級変更・役割変更・削除）
- メンバー追加時に部署の設定が必須
- 社員番号変更・パスワードリセット（全権管理者のみ）
- メンバーデータのCSVエクスポート（名前・社員番号・部署・等級・店舗・エリア・進捗率・テスト点数・合否）
- 部署・エリア・店舗管理（追加・削除・トグル表示・店舗のエリア間移動）
- カテゴリ・項目管理（部署×等級ごとに整理・トグル表示）
- テスト管理（部署×等級ごとに整理・トグル表示）
- 記述式テストの採点
- 再試験管理
- カテゴリ複製機能（manager_admin のみ）

### 共通機能
- ログイン機能（役割に応じて画面を切り替え）
- ログアウト機能（全画面共通）
- データはSupabaseに保存（リロードしても消えない）
- スマートフォン対応（member_top.html・test_page.html）

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
| name | text | カテゴリ名（**ユニーク制約なし**・部署ごとに独立） |
| department_id | int8 | 旧カラム（現在は category_departments を使用） |

**重要：** `categories.name` のユニーク制約は解除済み。
同じ名前でも部署が違えば別レコードとして独立管理される。

### category_departments（作成済み）
| カラム名 | 型 | 内容 |
|---------|-----|------|
| id | int8 | 自動採番 |
| category_id | int8 | カテゴリID（categories.id） |
| department_id | int8 | 部署ID（departments.id） |

**ユニーク制約：** `category_id` + `department_id`
**ON DELETE CASCADE：** カテゴリ削除時に自動で紐づけも削除される

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
| ログイン画面 | login.html | ✅ 完成 |
| メンバー：トップ | member_top.html | ✅ 完成（スマホ対応済み） |
| メンバー：テスト | test_page.html | ✅ 完成（スマホ対応済み） |
| MG：ダッシュボード | manager_dashboard.html | ✅ 完成 |
| MG：メンバー詳細 | member_detail.html | ✅ 完成（PDF出力対応済み） |
| 管理者：管理画面 | admin.html | ✅ 完成 |

---

## 8. ログイン・権限管理

### 役割と画面の対応
| role（DB値） | 表示名 | ログイン後の遷移先 | 権限範囲 |
|------|--------|-----------------|---------|
| member | メンバー | member_top.html | 自分のみ |
| manager | MG | manager_dashboard.html | 全部署ダッシュボード閲覧 |
| manager_admin | 全権管理者 | admin.html | 全部署閲覧＋全管理機能＋社員番号変更・PW変更 |
| dept_admin | 部署管理者 | manager_dashboard.html | 自部署のみ閲覧・管理 |

**注意：** `admin` ロールは廃止済み。既存の admin ユーザーは SQL で manager_admin に移行済み。

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
| admin.html | 部署・エリア・店舗管理 | 自部署のみ表示。部署の追加・削除ボタンは非表示 |
| admin.html | カテゴリ・項目管理 | 自部署に紐づくカテゴリのみ表示 |
| admin.html | テスト管理 | 自部署に紐づくカテゴリの問題集のみ表示 |

### フィルターのロジック
```javascript
var currentRole      = null;  // ログインユーザーの役割
var currentDeptId    = null;  // dept_admin の department_id
var allowedMemberIds = null;  // null=全員 / 配列=自部署メンバーIDのみ
```

### ダッシュボードと管理画面のフィルター差異（重要）
| 画面 | フィルター方式 | 対象ロール |
|------|-------------|---------|
| ダッシュボード（manager_dashboard） | 店舗名→エリア→部署の階層で絞り込み | member のみ |
| 管理画面メンバー一覧（admin） | `department_id` で直接フィルタ | member / manager / dept_admin |

```javascript
// 管理画面の initDeptFilter()：department_id で直接取得
var { data: deptMembers } = await client
  .from('user_profiles')
  .select('id')
  .eq('department_id', currentDeptId)
  .in('role', ['member', 'manager', 'dept_admin']);
allowedMemberIds = (deptMembers || []).map(function(m) { return m.id; });
```

---

## 12. カテゴリ管理の仕様

### 部署ごとの独立管理
- カテゴリは**部署ごとに完全に独立**したレコードとして管理する
- 同じ名前のカテゴリでも、部署が異なれば別々のレコード・別々の項目を持つ
- `categories.name` のユニーク制約は解除済み
- カテゴリ追加時は選択した部署ごとに新しいレコードを INSERT する

### カテゴリ複製（manager_admin のみ）
- カテゴリカードのヘッダーに「複製」ボタンが表示される
- 複製先の部署を選択すると、カテゴリ・全項目・全ステップをまるごとコピーする
- 複製後は完全に独立したレコードとなり、元カテゴリとは別々に管理できる

---

## 13. UIの仕様

### トグルタブ（admin.html）
- 部署カード・等級セクション・カテゴリカード・エリアカードのヘッダーをクリックすると開閉する
- カテゴリ管理・テスト管理・部署エリア店舗管理すべてに適用済み
- `toggleSection(bodyId)` 関数で制御
- データ追加・削除後の再描画時、開いていたセクションの状態を復元する

```javascript
function toggleSection(bodyId) {
  var el = document.getElementById(bodyId);
  var arrow = document.getElementById('arr-' + bodyId);
  if (el.style.display === 'none') {
    el.style.display = '';
    arrow.classList.remove('closed');
  } else {
    el.style.display = 'none';
    arrow.classList.add('closed');
  }
}
```

### 部署・エリア・店舗管理のUI仕様
- 事業部ヘッダー：`Nエリア・N店舗` のカウントを表示
- エリアヘッダー：`N店舗` のカウントを表示
- 店舗行：「移動」ボタンで移動先エリアを選択して即時反映（エリアは部署でグルーピング）
- 削除ボタンはトグルに連動しないよう `event.stopPropagation()` を付与

### ダッシュボードのエリア・店舗展開
- エリア別・店舗別タブの各行をクリックするとその配下のメンバー一覧がアコーディオン展開される
- 展開されるサブテーブル：名前 / 等級 / 進捗率 / テスト点数 / 合否 / 詳細ボタン
- `toggleExpand(rowId)` 関数で制御

### ダッシュボードの管理画面ボタン
- `dept_admin` / `manager_admin` でログインするとヘッダーに「⚙️ 管理画面」ボタンが表示される
- 昇級申請ボタンと同様のスタイル（背景あり・太字・アイコン付き）

```css
.admin-link {
  display: inline-flex; align-items: center; gap: 5px;
  padding: 5px 14px;
  background: rgba(255,255,255,0.22); border: 1px solid rgba(255,255,255,0.55);
  border-radius: 6px; color: #fff; font-size: 12px; font-weight: 700;
  text-decoration: none;
}
```

### メンバー一覧テーブル（admin.html）
- 列順：名前 | 社員番号 | 部署 | 店舗 | 等級 | 役割 | 操作
- PCでは横スクロールなし（`table-layout: fixed; width: 100%` + 列幅を%で割り当て）
- スマホ（768px以下）は横スクロール維持
- 「📥 CSVエクスポート」ボタンでメンバーデータをCSVダウンロード
- 全権管理者のみ：各行に「社員番号」「PW変更」ボタンを表示

### メンバー追加モーダル（admin.html）
- 部署選択は**全ロールで必須**（赤アスタリスク表示）
- 全権管理者が追加する場合：全部署からプルダウン選択（「選択してください」がデフォルト）
- 部署管理者が追加する場合：自部署を自動セット・変更不可（グレー背景で固定表示）
- 部署未選択のまま「追加する」を押すとアラートを表示

### CSVエクスポート仕様
- 列順：名前, 社員番号, 部署, 等級, 店舗, エリア, 進捗率(%), テスト点数, 合否
- BOM付き（`\uFEFF`）でExcel日本語対応
- dept_adminの場合は自部署メンバーのみ出力
- テスト点数・合否は直近の受験記録を使用、未受験は「未受験」と表示

### メンバー詳細のPDF出力
- 「📄 PDFで出力」ボタン（パンくずの右端）をクリック
- ブラウザの印刷ダイアログが開く → 「PDFとして保存」でPDF化
- 印刷時：ヘッダー・コメント入力欄・ボタン類を非表示
- 印刷時：「メンバー詳細レポート」と出力日付のヘッダーを表示
- `@media print` で `print-color-adjust: exact` を設定（背景色も印刷）

### スマホ対応（member_top.html・test_page.html）
- `@media (max-width: 640px)` でレイアウト変更
- サイドバー → 横スクロール型タブバーに変換
- ボタン・フォントサイズ・余白をタッチ操作しやすいサイズに調整

---

## 14. 社員番号変更・パスワードリセット（全権管理者のみ）

- メンバー一覧の各行に「社員番号」「PW変更」ボタンを表示（manager_admin のみ表示）
- **社員番号変更**：`user_profiles.employee_id` と Supabase Auth のメールアドレス（`社員番号@education-app.com`）を両方更新
- **パスワードリセット**：`adminClient.auth.admin.updateUserById()` で任意のパスワードに変更

```javascript
// 社員番号変更
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });

// パスワードリセット
await adminClient.auth.admin.updateUserById(userId, { password: newPw });
```

---

## 15. データ削除順序（重要）

Supabase SQL でデータを削除する場合、FK制約により以下の順番を守ること。

```sql
-- 子テーブルから順に削除
DELETE FROM text_answers;
DELETE FROM test_results;
DELETE FROM check_progress;
DELETE FROM comments;
DELETE FROM retest_permissions;
DELETE FROM grade_up_requests;
DELETE FROM test_choices;       -- test_questions より先
DELETE FROM test_questions;
DELETE FROM test_sets;
DELETE FROM check_steps;
DELETE FROM check_items;
DELETE FROM category_departments;
DELETE FROM categories;
DELETE FROM stores;
DELETE FROM areas;
-- user_profiles は最後
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
| 2026-04-08 | フェーズ2〜4完了 |
| 2026-04-08 | フェーズ5開始。管理画面の全機能実装・コメント機能実装 |
| 2026-04-08 | 昇級申請機能（grade_up_requests）実装完了 |
| 2026-04-08 | 部署管理機能追加（departments テーブル・3階層表示） |
| 2026-04-08 | 役割に dept_admin（部署管理者）を追加 |
| 2026-04-09 | Cowork（Claude）に開発環境を移行 |
| 2026-04-09 | dept_admin フィルター実装（manager_dashboard・admin 両画面） |
| 2026-04-09 | category_departments 中間テーブルを作成（多対多対応） |
| 2026-04-09 | カテゴリを部署ごとに独立管理に変更（ユニーク制約解除） |
| 2026-04-09 | カテゴリ複製機能を追加（manager_admin のみ） |
| 2026-04-09 | カテゴリ・等級・部署カードにトグル開閉機能を追加 |
| 2026-04-09 | 項目追加モーダルにステップ同時追加機能を追加 |
| 2026-04-09 | ダッシュボードのエリア・店舗行クリックでメンバー一覧展開を追加 |
| 2026-04-09 | member_top.html・test_page.html にスマホ対応CSS追加 |
| 2026-04-09 | admin.html メンバー一覧にCSVエクスポート機能を追加 |
| 2026-04-09 | メンバー一覧に部署列を追加（社員番号と店舗の間） |
| 2026-04-09 | member_detail.html にPDF出力機能を追加（@media print） |
| 2026-04-09 | 部署・エリア・店舗管理画面にトグルタブを追加 |
| 2026-04-09 | トグル状態をデータ追加後も維持するよう修正 |
| 2026-04-09 | 事業部ヘッダーに店舗数を追加（Nエリア・N店舗） |
| 2026-04-09 | 店舗のエリア間移動機能を追加（移動ボタン＋モーダル） |
| 2026-04-09 | メンバー一覧テーブルのデザイン修正（横スクロール・select幅制限） |
| 2026-04-09 | ダミーデータをクリーンアップ（3名のみ残す） |
| 2026-04-09 | v13仕様書・引継書を作成。GitHub Pages 公開に着手 |
| 2026-04-09 | 「上司」→「MG」にロール表記変更 |
| 2026-04-09 | `admin` ロール廃止・`manager_admin` に統合（SQL移行済み） |
| 2026-04-09 | 全権管理者のみが manager_admin ロールを付与可能に制限 |
| 2026-04-09 | manager_admin がダッシュボードから管理画面に飛べないバグを修正 |
| 2026-04-09 | アプリ名を「AZism 教育進捗管理」に変更（全ファイル） |
| 2026-04-09 | 社員番号変更・パスワードリセット機能を追加（manager_admin のみ） |
| 2026-04-09 | メンバー一覧テーブルをPC画面では横スクロールなしで表示されるよう修正 |
| 2026-04-09 | メンバー追加時の部署設定を全ロールで必須化 |
| 2026-04-09 | 部署管理者が追加時は自部署を自動セット・変更不可に変更 |
| 2026-04-09 | ダッシュボードの管理画面リンクを⚙️アイコン付きボタンデザインに変更 |
| 2026-04-09 | 管理画面メンバー一覧のdept_adminフィルターをdepartment_id直接参照に変更 |
| 2026-04-09 | v14仕様書・引継書を作成 |
