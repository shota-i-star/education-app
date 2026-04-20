# 引継書 — AZism 教育進捗管理
作成日：2026年4月20日（v21）

---

## ▼ 引継書の使い方
新しい会話を始めるときは、この引継書と仕様書を**両方**冒頭に添付してから作業を開始してください。

---

## 1. 完了したこと

### フェーズ1〜5（完了）
- 全6画面の作成・Supabase連携・ログイン機能・集計・グラフ
- 管理画面の全機能・コメント機能・ステップ式チェック・再試験・昇級申請
- 部署管理機能（3階層表示）・dept_admin フィルター・カテゴリ複製
- スマホ対応（member_top / test_page）

### フェーズ6（GitHub Pages 公開・継続）
- GitHub Pages 公開済み（静的ホスティング）
- ダミーデータをクリーンアップ（3名のみ残す）

### v15〜v19 で追加・修正した機能
（詳細は各バージョンの引継書を参照）

### v20 で追加・修正した機能
（詳細は handover_v20.md を参照）

### v21 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| 問題集の編集機能 | admin.html | 「編集」ボタン追加 + `edit-testset-modal` 新設。問題集名・部署・カテゴリ・等級・合格基準点を変更可能 |
| 問題の編集機能 | admin.html | 「編集」ボタン追加 + `edit-question-modal` 新設。問題文・形式・配点・選択肢・正解を変更可能 |
| 問題一覧レイアウト修正 | admin.html | 問題文セルを `width:100%` にして残り列（形式・配点・編集・削除）を常に右端に固定 |

---

## 2. 現在取り組み中の作業

GitHub Pages での継続的な機能改善・運用

---

## 3. ⚠️ 次回作業前に必要なSupabase設定

**v21 では DB の変更なし。**

**v20 で追加したカラムがまだ未実行の場合は、Supabase SQL Editor で以下を実行してください：**

```sql
-- test_results に問題集ID・回答JSONを追加
ALTER TABLE test_results ADD COLUMN IF NOT EXISTS test_set_id int8 REFERENCES test_sets(id);
ALTER TABLE test_results ADD COLUMN IF NOT EXISTS answers jsonb;

-- text_answers に問題集ID・問題IDを追加
ALTER TABLE text_answers ADD COLUMN IF NOT EXISTS test_set_id int8 REFERENCES test_sets(id);
ALTER TABLE text_answers ADD COLUMN IF NOT EXISTS question_id int8 REFERENCES test_questions(id);

-- retest_permissions に問題集IDを追加
ALTER TABLE retest_permissions ADD COLUMN IF NOT EXISTS test_set_id int8 REFERENCES test_sets(id);
```

**v19 以前のSQL（未実行の場合のみ）**
```sql
ALTER TABLE categories  ADD COLUMN IF NOT EXISTS sort_order int DEFAULT null;
ALTER TABLE check_items ADD COLUMN IF NOT EXISTS sort_order int DEFAULT null;
```

---

## 4. GitHubへのアップロード手順

1. GitHub リポジトリページを開く
2. 更新したファイルを選択してアップロード（上書き）
3. 「Commit changes」をクリック
4. 数秒〜1分で GitHub Pages に反映される

**v21 で変更したファイル（アップロード対象）：**
- `admin.html`（問題集編集モーダル・問題編集モーダル追加、問題一覧レイアウト修正）
- `spec_doc/spec_doc v21.md`（仕様書更新）
- `handover/handover_v21.md`（引継書更新）

---

## 5. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成（PWA対応・ロゴ追加）
├── member_top.html         ✅ 完成（テスト選択モーダル・採点待ちブロック・v20〜）
├── test_page.html          ✅ 完成（動的テスト・3種問題タイプ・合否ロジック・v20〜）
├── manager_dashboard.html  ✅ 完成（2軸進捗・等級別タブ・全体進捗サマリー・PWA対応）
├── member_detail.html      ✅ 完成（テスト履歴に問題集名・合計点・採点中対応・v20〜）
├── admin.html              ✅ 完成（問題集・問題の編集機能追加・問題行レイアウト修正・v21〜）
├── manifest.json           📱 PWAマニフェスト
├── AZ-GROUP.jpg            🖼 旧ロゴ原画
├── AZ icon.png             🖼 新ロゴ原画（v18〜）
├── image/
│   ├── favicon.ico
│   ├── favicon.png
│   ├── apple-touch-icon.png
│   ├── icon-192.png
│   ├── icon-512.png
│   └── logo-white.png
├── csv/
│   ├── csv_template_members.csv  （エリア列削除・v20〜）
│   ├── csv_template_categories.csv
│   ├── csv_template_stores.csv   （列順変更・v20〜）
│   └── csv_template_items.csv
├── spec_doc/
│   └── spec_doc v21.md     📄 仕様書（最新）
└── handover/
    └── handover_v21.md     📄 引継書（最新）
```

---

## 6. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | OneDrive/デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |
| AIコーディング | Cowork（Claude）にフォルダを接続して直接編集 |

---

## 7. Supabase情報

| 項目 | 値 |
|------|------|
| Project URL | `https://rkclfgpewmjdluwufjng.supabase.co` |
| anon key | コード内の `SUPABASE_ANON_KEY` に記載 |
| service role key | コード内の `SUPABASE_SERVICE_KEY` に記載（admin.htmlのみ） |
| SDKバージョン | `@2.39.3`（固定） |

---

## 8. データベース一覧

| テーブル名 | 内容 |
|-----------|------|
| user_profiles | メンバー情報・役割・等級・department_id |
| check_progress | チェック状況（step_idで管理） |
| check_items | チェック項目（sort_order カラムあり） |
| check_steps | チェックステップ |
| test_results | テスト結果（test_set_id・answers JSONB 追加・v20〜） |
| text_answers | 記述式回答・採点（test_set_id・question_id 追加・v20〜） |
| test_sets | 問題集（name・category_id・grade・pass_score） |
| test_questions | テスト問題（type: choice/truefalse/text・point） |
| test_choices | 選択肢（○×は body='maru'/'batu' + is_correct） |
| comments | MGコメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
| stores | 店舗（area_idでエリアに紐づく） |
| categories | チェックカテゴリ（sort_orderあり） |
| category_departments | カテゴリ×部署の中間テーブル |
| retest_permissions | 再試験許可（test_set_id 追加・v20〜） |
| grade_up_requests | 昇級申請 |

---

## 9. 重要な実装メモ

### 役割一覧
| role（DB値） | 表示名 | 権限 |
|---|---|---|
| member | メンバー | 自分の学習画面のみ |
| manager | MG | 全部署のダッシュボード閲覧 |
| manager_admin | 全権管理者 | 全部署閲覧＋全管理機能＋社員番号変更・PW変更・部署/エリア名編集 |
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理 |

※ `admin` ロールは廃止済み（`UPDATE user_profiles SET role='manager_admin' WHERE role='admin'`）

### ログイン後の遷移先
```javascript
if (role === 'member') → member_top.html
if (role === 'manager' || role === 'dept_admin') → manager_dashboard.html
if (role === 'manager_admin') → admin.html
```

---

### 問題集の編集機能（admin.html・v21〜）

**モーダル ID：** `edit-testset-modal`
**フィールド：** `edit-testset-name` / `edit-testset-dept` / `edit-testset-category` / `edit-testset-grade` / `edit-testset-pass`

```javascript
var currentEditTestSetId = null;

async function openEditTestSetModal(setId) {
  // 1. test_sets から現在の値を取得してフォームへプリセット
  // 2. category_departments から現在のカテゴリの部署を逆引きして部署セレクトを選択
  // 3. updateEditTestSetCategories() でカテゴリ絞り込み後、category を選択
  document.getElementById('edit-testset-modal').classList.add('show');
}

function updateEditTestSetCategories() {
  // 選択中の部署 ID から category_departments → categories を絞り込み
}

async function saveTestSet() {
  // test_sets を UPDATE
  // カテゴリ・等級変更時は配下の test_questions.category_id / grade も UPDATE
  await client.from('test_sets').update({ name, category_id, grade, pass_score })
    .eq('id', currentEditTestSetId);
  await client.from('test_questions').update({ category_id, grade })
    .eq('test_set_id', currentEditTestSetId);
}
```

---

### 問題の編集機能（admin.html・v21〜）

**モーダル ID：** `edit-question-modal`
**フィールド：** `edit-question-text` / `edit-question-type` / `edit-question-point`
**選択肢フィールド：** `ec1-text`〜`ec4-text` / `ec1-correct`〜`ec4-correct`（4択用）
**○×フィールド：** `input[name="edit-tf-correct"]`（value: `maru` / `batu`）

```javascript
var currentEditQuestionId = null;

function toggleEditChoices() {
  var type = document.getElementById('edit-question-type').value;
  document.getElementById('edit-choices-area').style.display = type === 'choice'    ? 'block' : 'none';
  document.getElementById('edit-tf-area').style.display      = type === 'truefalse' ? 'block' : 'none';
}

async function openEditQuestionModal(qId) {
  // 1. test_questions から問題文・形式・配点を取得してフォームへプリセット
  // 2. test_choices から選択肢を取得
  //    choice: ec1〜ec4 に body・is_correct をプリセット
  //    truefalse: is_correct=true の body（maru/batu）をラジオ選択
  // 3. toggleEditChoices() で表示切替
  document.getElementById('edit-question-modal').classList.add('show');
}

async function saveQuestion() {
  // test_questions を UPDATE（question, type, point）
  // test_choices を全削除後、タイプに応じて再 INSERT
  //   choice: 空でない選択肢のみ INSERT
  //   truefalse: maru/batu の 2 行を INSERT（is_correct は選択値で判定）
  //   text: 選択肢なし
}
```

---

### 問題一覧テーブルのレイアウト（admin.html・v21〜）

テーブルは **5列構成**：問題文 / 形式 / 配点 / 編集 / 削除

```html
<table>
  <thead>
    <tr>
      <th style="width:100%">問題文</th>
      <th style="text-align:right">形式</th>
      <th style="text-align:right">配点</th>
      <th style="text-align:right"></th>  <!-- 編集 -->
      <th style="text-align:right"></th>  <!-- 削除 -->
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="width:100%;white-space:normal;word-break:break-word">問題文</td>
      <td style="text-align:right;white-space:nowrap"><span class="pill ...">4択</span></td>
      <td style="text-align:right;white-space:nowrap">10点</td>
      <td style="text-align:right;white-space:nowrap"><button ... onclick="openEditQuestionModal(id)">編集</button></td>
      <td style="text-align:right;white-space:nowrap"><button ... onclick="deleteQuestion(id)">削除</button></td>
    </tr>
  </tbody>
</table>
```

**ポイント：**
- 問題文セルに `width:100%` → 問題文が残りの横幅をすべて占有
- 右4列は `white-space:nowrap` で折り返し禁止 → 常に右端に固定
- 問題文は `white-space:normal; word-break:break-word` で長文も折り返す

---

### テスト合否の計算ロジック（v20〜）

```javascript
var textSumBySet = {};
var textUngradedBySet = {};
textAnswers.forEach(function(t) {
  if (t.score === null) {
    textUngradedBySet[t.test_set_id] = true;
  } else {
    textSumBySet[t.test_set_id] = (textSumBySet[t.test_set_id] || 0) + t.score;
  }
});

var isPending  = !!textUngradedBySet[testSetId];
var totalScore = (result.score || 0) + (textSumBySet[testSetId] || 0);
var passed     = totalScore >= testSet.pass_score;
```

---

### test_results.passed の保存方式（v20〜 重要）

test_results.passed は **NOT NULL** 制約があるため、記述式ありテストでも null ではなく `false` を保存する。
合否の最終判定は表示時に「合計点 ≥ pass_score」で行うため、DB 上の passed 値は参照しない。

```javascript
var passedStored = hasText ? false : (autoScore >= TEST_SET.pass_score);
```

---

### 再受験ブロックの実装（test_page.html・v20〜）

```javascript
// ページ読み込み時
IS_PENDING_GRADING = !!(ungradedTexts && ungradedTexts.length > 0);
if (latest || IS_PENDING_GRADING) {
  var canRetest = IS_PENDING_GRADING ? false : await checkRetestPermission(setId);
  if (canRetest) { renderQuestions(false); }
  else { SUBMITTED = true; renderQuestions(true); if (latest) await showPastResult(latest); }
}

// 送信時（3段ガード）
if (SUBMITTED) return;                          // ① フラグ
// ② text_answers に未採点行があればブロック
// ③ test_results が存在し未使用 retest_permissions もなければブロック
```

---

### ○×（truefalse）問題の DB 構造（v20〜）

```
test_choices に 2 行：
  { question_id, body: 'maru', is_correct: true/false }
  { question_id, body: 'batu', is_correct: false/true }
```

問題追加・編集モーダルで正解ラジオを選択すると is_correct が決まる。

---

### 問題集追加・編集時の部署→カテゴリ絞り込み（admin.html・v20〜）

```javascript
// 追加: updateTestSetCategories() / 編集: updateEditTestSetCategories()
// どちらも同じ仕組み：
// 選択中の dept.id → category_departments → categories でフィルタリング
```

---

### 採点ページの配点表示（admin.html・v20〜）

```javascript
var maxPoint = (pointByQ[row.question_id] != null) ? pointByQ[row.question_id] : 100;
// input: placeholder="0〜{maxPoint}" max="{maxPoint}" style="width:110px"
// span: / {maxPoint}点
```

---

### CSV インポートの重要仕様（v20〜）

**メンバー CSV**：`名前,社員番号,パスワード,部署名,店舗名(任意),等級(0〜5),役割`
- エリアは店舗 → area_id → areas で自動解決

**店舗 CSV**：`部署名,店舗名,エリア名`
- 部署名 → department_id → areas（同部署内）でエリアを特定

---

### CSVインポートの文字コード自動判別（v18〜）
```javascript
if (bytes[0]===0xEF && bytes[1]===0xBB && bytes[2]===0xBF) {
  text = new TextDecoder('utf-8').decode(buffer).slice(1);      // UTF-8 BOM
} else if (bytes[0]===0xFF && bytes[1]===0xFE) {
  text = new TextDecoder('utf-16le').decode(buffer);            // UTF-16 LE
} else {
  try { text = new TextDecoder('shift-jis').decode(buffer); }
  catch(e) { text = new TextDecoder('utf-8').decode(buffer); }  // Shift-JIS / UTF-8
}
```

---

### データ削除の安全な順序（FK制約）
```sql
DELETE FROM text_answers;
DELETE FROM test_results;
DELETE FROM check_progress;
DELETE FROM comments;
DELETE FROM retest_permissions;
DELETE FROM grade_up_requests;
DELETE FROM test_choices;    -- ← test_questions より必ず先
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

### アポストロフィ対策
onclick属性内の名前文字列には `.replace(/'/g,"&#39;")` を適用すること。

### 社員番号変更・PW変更
```javascript
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });
await adminClient.auth.admin.updateUserById(userId, { password: newPw });
```

---

## 10. 次の会話での引継ぎ指示

```
添付の仕様書（spec_doc v21.md）と引継書（handover_v21.md）をもとに、
AZism 教育進捗管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
