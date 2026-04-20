# 引継書 — AZism 教育進捗管理
作成日：2026年4月18日（v20）

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

### v15〜v18 で追加・修正した機能
（詳細は各バージョンの引継書を参照）

### v19 で追加・修正した機能
（詳細は handover_v19.md を参照）

### v20 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| テスト画面の全面刷新 | test_page.html | ダミー固定データを廃止し、?set_id=N で DB から動的読み込み |
| 問題タイプ3種対応 | test_page.html / admin.html | 4択・○×・記述を実装。○×は choices テーブルで maru/batu + is_correct |
| 合否ロジック変更 | test_page.html / member_top.html / admin.html | 選択式点＋記述式採点済み合計 ≥ pass_score で合格 |
| 採点中ステータス | test_page.html / member_top.html / admin.html | 記述式あり提出後は「採点中」表示、採点完了まで合否確定しない |
| 再受験ブロック強化 | test_page.html | SUBMITTED フラグ＋DB 存在チェックの2段ガード。採点中は絶対に受験不可 |
| passed 保存方式修正 | test_page.html | 記述式ありは passed=false で保存（NOT NULL 制約対応）。表示は合計点で再計算 |
| テスト選択モーダル | member_top.html | テストボタン → 問題集一覧モーダル → 選択 → test_page.html |
| 採点待ちブロック | member_top.html | 採点中のボタンを「採点待ち」ラベルに変更 |
| 問題集追加フロー改善 | admin.html | 部署選択 → その部署のカテゴリのみ絞り込み |
| 採点ページ配点表示 | admin.html | test_questions.point を取得し入力欄の最大値に反映。入力欄幅も拡張 |
| 再試験管理の修正 | admin.html | 採点中（未採点 text_answers あり）を対象外に。test_set_id 単位で管理 |
| 店舗ボタン右寄せ | admin.html | 部署・エリア・店舗管理の店舗行の移動・削除ボタンを右揃えに変更 |
| メンバー詳細テスト履歴 | member_detail.html | 問題集名・合計点（選択式＋記述式）・採点中ステータスを表示 |
| メンバー CSV 修正 | csv_template_members.csv | エリア名列を削除（店舗から自動解決）。列順を管理画面表示順に統一 |
| 店舗 CSV 修正 | csv_template_stores.csv | 列順を 部署名→店舗名→エリア名 に変更。部署名から area を自動解決 |

---

## 2. 現在取り組み中の作業

GitHub Pages での継続的な機能改善・運用

---

## 3. ⚠️ 次回作業前に必要なSupabase設定

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

**v20で変更したファイル（アップロード対象）：**
- `test_page.html`（全面刷新・動的テスト読み込み・3種問題タイプ・合否ロジック・再受験ブロック）
- `member_top.html`（テスト選択モーダル・採点待ちブロック・ステータス表示）
- `admin.html`（問題集追加フロー・採点ページ配点表示・再試験管理修正・店舗ボタン右寄せ）
- `member_detail.html`（テスト履歴に問題集名・合計点・採点中ステータス追加）
- `csv/csv_template_members.csv`（エリア列削除・列順変更）
- `csv/csv_template_stores.csv`（列順変更）
- `spec_doc/spec_doc v20.md`（仕様書更新）
- `handover/handover_v20.md`（引継書更新）

---

## 5. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成（PWA対応・ロゴ追加）
├── member_top.html         ✅ 完成（テスト選択モーダル・採点待ちブロック・v20〜）
├── test_page.html          ✅ 完成（動的テスト・3種問題タイプ・合否ロジック・v20〜）
├── manager_dashboard.html  ✅ 完成（2軸進捗・等級別タブ・全体進捗サマリー・PWA対応）
├── member_detail.html      ✅ 完成（テスト履歴に問題集名・合計点・採点中対応・v20〜）
├── admin.html              ✅ 完成（全管理機能・問題集追加フロー改善・採点配点表示・v20〜）
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
│   └── spec_doc v20.md     📄 仕様書（最新）
└── handover/
    └── handover_v20.md     📄 引継書（最新）
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
| test_results | テスト結果（**test_set_id・answers JSONB 追加・v20〜**） |
| text_answers | 記述式回答・採点（**test_set_id・question_id 追加・v20〜**） |
| test_sets | 問題集（name・category_id・grade・pass_score） |
| test_questions | テスト問題（type: choice/truefalse/text・point） |
| test_choices | 選択肢（○×は body='maru'/'batu' + is_correct） |
| comments | MGコメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
| stores | 店舗（area_idでエリアに紐づく） |
| categories | チェックカテゴリ（sort_orderあり） |
| category_departments | カテゴリ×部署の中間テーブル |
| retest_permissions | 再試験許可（**test_set_id 追加・v20〜**） |
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

### テスト合否の計算ロジック（v20〜）

```javascript
// 合計点 = 選択式自動採点 + 記述式採点済み合計
var textSumBySet = {};
var textUngradedBySet = {};
textAnswers.forEach(function(t) {
  if (t.score === null) {
    textUngradedBySet[t.test_set_id] = true;
  } else {
    textSumBySet[t.test_set_id] = (textSumBySet[t.test_set_id] || 0) + t.score;
  }
});

// 採点中判定
var isPending = !!textUngradedBySet[testSetId];

// 最終合否（採点完了後）
var totalScore = (result.score || 0) + (textSumBySet[testSetId] || 0);
var passed = totalScore >= testSet.pass_score;
```

---

### test_results.passed の保存方式（v20〜 重要）

test_results.passed は **NOT NULL** 制約があるため、記述式ありテストでも null ではなく `false` を保存する。
合否の最終判定は表示時に「合計点 ≥ pass_score」で行うため、DB 上の passed 値は参照しない。

```javascript
// submitTest 内
var passedStored = hasText ? false : (autoScore >= TEST_SET.pass_score);
// DB には passedStored を保存。表示は合計点で再計算
```

---

### 再受験ブロックの実装（test_page.html・v20〜）

```javascript
// loadTest 内：ページ読み込み時に判定
var latest = (results && results[0]) ? results[0] : null;

var { data: ungradedTexts } = await client
  .from('text_answers').select('id')
  .eq('user_id', CURRENT_USER).eq('test_set_id', setId)
  .is('score', null).limit(1);
IS_PENDING_GRADING = !!(ungradedTexts && ungradedTexts.length > 0);

if (latest || IS_PENDING_GRADING) {
  var canRetest = IS_PENDING_GRADING ? false : await checkRetestPermission(setId);
  if (canRetest) {
    // 再試験許可あり → 受験可能
    renderQuestions(false);
  } else {
    // 採点中 or 受験済み → 閲覧のみ
    SUBMITTED = true;
    renderQuestions(true);
    if (latest) await showPastResult(latest);
  }
}

// submitTest 内：送信時にも二重チェック
if (SUBMITTED) return;  // ① フラグチェック

// ② 採点中チェック（text_answers）
var { data: dupTexts } = await client.from('text_answers')...is('score', null)...
if (dupTexts && dupTexts.length > 0) { alert('採点中のため再受験不可'); return; }

// ③ 既受験チェック（test_results + retest_permissions）
var { data: existResults } = await client.from('test_results')...
if (existResults && existResults.length > 0) {
  var { data: unusedPerms } = await client.from('retest_permissions')...is('used_at', null)...
  if (!unusedPerms || unusedPerms.length === 0) { alert('受験済み'); return; }
}
```

---

### テスト選択モーダル（member_top.html・v20〜）

```javascript
async function openTestSelectModal(catId, catName) {
  // カテゴリの問題集一覧を取得
  var sets = TEST_SETS_BY_CAT[catId] || [];

  // 受験履歴・text_answers・retest_permissions を取得して状態判定
  // 採点中判定を最優先で評価
  if (ungraded) {
    statusHtml = '採点中';   btnLabel = '採点待ち';
  } else if (!latest) {
    statusHtml = '未受験';   btnLabel = '受ける';
  } else if (finalPassed) {
    statusHtml = '合格 X点'; btnLabel = '結果を見る';
  } else if (permitted) {
    statusHtml = '不合格 X点'; btnLabel = '再試験';
  } else {
    statusHtml = '不合格 X点'; btnLabel = '結果を見る';
  }
  // 全ボタン: onclick="location.href='test_page.html?set_id=N'"
}
```

---

### ○×（truefalse）問題の DB 構造（v20〜）

test_choices テーブルに 2 行を登録する方式：

| question_id | body | is_correct |
|-------------|------|-----------|
| 問題ID | 'maru' | true（○が正解の場合） |
| 問題ID | 'batu' | false |

管理画面の問題追加モーダルで「○が正解」「×が正解」のラジオボタンを選択すると自動挿入される。

```javascript
// admin.html addQuestion 内
if (type === 'truefalse') {
  var tfCorrect = document.querySelector('input[name="tf-correct"]:checked').value; // 'maru' or 'batu'
  await client.from('test_choices').insert([
    { question_id: qId, body: 'maru', is_correct: tfCorrect === 'maru' },
    { question_id: qId, body: 'batu', is_correct: tfCorrect === 'batu' }
  ]);
}
```

---

### 問題集追加時の部署→カテゴリ絞り込み（admin.html・v20〜）

```javascript
async function openAddTestSetModal() {
  // 1. 部署一覧を表示
  // 2. 部署選択時に updateTestSetCategories() を呼ぶ
}

async function updateTestSetCategories() {
  var deptId = parseInt(document.getElementById('ts-dept').value);
  // category_departments から deptId に紐づく category_id を取得
  // categories からその category_id のカテゴリのみ表示
}
```

---

### 採点ページの配点表示（admin.html・v20〜）

```javascript
async function loadAnswers() {
  var { data } = await client.from('text_answers').select('*')...;

  // 問題ごとの配点を取得
  var qIds = data.map(r => r.question_id).filter(v => v != null);
  var pointByQ = {};
  if (qIds.length > 0) {
    var { data: qs } = await client.from('test_questions').select('id, point').in('id', qIds);
    qs.forEach(q => { pointByQ[q.id] = q.point || 0; });
  }

  // 各行の入力欄に配点を反映
  var maxPoint = pointByQ[row.question_id] ?? 100;
  // → placeholder="0〜{maxPoint}"  max="{maxPoint}"  / {maxPoint}点
}
```

---

### 再試験管理のロジック（admin.html・v20〜）

```javascript
async function loadRetest() {
  // 全 test_results・text_answers を取得
  // 採点中（text_answers に score=null あり）→ 除外
  // 合計点（score + text合計）< pass_score → 再試験対象
  var filteredResults = latestResults.filter(function(r) {
    var key = r.user_id + '_' + r.test_set_id;
    if (textUngradedBySet[key]) return false;            // 採点中は除外
    var ts = testSets.find(s => s.id === r.test_set_id);
    if (!ts) return false;
    var total = (r.score || 0) + (textSumBySet[key] || 0);
    return total < ts.pass_score;                         // 合計点 < 合格点 のみ
  });
}
```

---

### メンバー詳細のテスト履歴（member_detail.html・v20〜）

```javascript
async function loadMemberDetail() {
  // test_results に test_set_id を含めて取得
  var { data: results } = await client.from('test_results')
    .select('id, category, grade, score, passed, taken_at, test_set_id')...;

  // text_answers を test_set_id 単位で集計
  var { data: textRows } = await client.from('text_answers')
    .select('test_set_id, score').eq('user_id', TARGET_USER_ID);
  // → textSumBySet / textUngradedBySet を構築

  // test_sets の name・pass_score を取得 → testSetById
}

function renderTestHistory(results, textSumBySet, textUngradedBySet, testSetById) {
  // 各行：カテゴリ / 問題集名 / 等級 / 受験日 / 合計点（採点中なら「採点中」）/ 合否
}
```

---

### CSV インポートの重要仕様

**メンバー CSV（v20〜）**
```
名前,社員番号,パスワード,部署名,店舗名(任意),等級(0〜5),役割(member/manager/dept_admin)
```
- エリア名列は廃止。店舗名 → stores テーブル → area_id → areas テーブル でエリアを自動解決
- 同名の店舗が別エリアにある場合、department_id でフィルタリングして一意に特定

**店舗 CSV（v20〜）**
```
部署名,店舗名,エリア名
```
- 部署名 → departments → department_id → areas（同部署のみ）でエリアを検索
- 同名エリアが別部署にある場合でも department_id フィルターで一意に特定

---

### CSVインポートの文字コード自動判別（v18〜）
```javascript
reader.readAsArrayBuffer(file);
reader.onload = function(e) {
  var buffer = e.target.result;
  var bytes  = new Uint8Array(buffer);
  var text;
  if (bytes[0]===0xEF && bytes[1]===0xBB && bytes[2]===0xBF) {
    text = new TextDecoder('utf-8').decode(buffer).slice(1);
  } else if (bytes[0]===0xFF && bytes[1]===0xFE) {
    text = new TextDecoder('utf-16le').decode(buffer);
  } else {
    try { text = new TextDecoder('shift-jis').decode(buffer); }
    catch(e) { text = new TextDecoder('utf-8').decode(buffer); }
  }
};
```

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
添付の仕様書（spec_doc v20.md）と引継書（handover_v20.md）をもとに、
AZism 教育進捗管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
