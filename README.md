# トレーニング予約調整システム

GitHub Pages + Firebase Firestore で動く無料のスケジュール調整 Web アプリです。

---

## セットアップ手順

### 1. Firebase プロジェクトを作成

1. [Firebase Console](https://console.firebase.google.com/) にアクセス
2. **「プロジェクトを追加」** → プロジェクト名を入力（例：gym-schedule）
3. 左メニュー **「Firestore Database」** → **「データベースの作成」**
4. **「テストモードで開始」** を選択 → ロケーションを選んで作成

### 2. Firebase 設定をコピー

1. プロジェクト設定（歯車アイコン）→ **「マイアプリ」** → **「＋アプリを追加」** → ウェブ `</>`
2. アプリのニックネームを入力 → **「アプリを登録」**
3. 表示される `firebaseConfig` の内容をコピー

### 3. index.html を書き換える

`index.html` の以下の部分を書き換えます：

```js
const FIREBASE_CONFIG = {
  apiKey: "実際のキーに変更",
  authDomain: "xxx.firebaseapp.com",
  projectId: "xxx",
  storageBucket: "xxx.appspot.com",
  messagingSenderId: "123456",
  appId: "1:123456:web:abc"
};
```

PIN を変更したい場合はその下の行を編集：

```js
const PINS = { client: '1234', trainer: '5678' };
```

### 4. GitHub Pages で公開

```bash
git add index.html
git commit -m "Add gym schedule app"
git push
```

1. GitHub リポジトリ → **Settings** → **Pages**
2. **Source** → `main` ブランチ、`/ (root)` を選択 → **Save**
3. 数分後に表示される URL がアプリの URL です

---

## 使い方

### クライアント（PIN: 1234）
- カレンダーの日付をタップ → 希望時間帯を追加
- トレーナーから代替提案が来たら **承諾** または **辞退**

### トレーナー（PIN: 5678）
- クライアントの希望を確認 → **確定する** or **代替を提案**
- 代替提案時は新しい日時とメモを入力

### ステータス
| 色 | 状態 |
|----|------|
| 🟣 紫 | 希望提出済み（未確定） |
| 🟢 緑 | 確定 |
| 🟠 橙 | トレーナーから代替提案あり |

---

## 特徴

- **リアルタイム同期** — トレーナーが操作するとクライアント画面に即反映
- **無料** — Firebase 無料枠で十分運用可能
- **スマホ対応** — モバイルファーストのレスポンシブ UI

---

## 🔒 セキュリティ設定（必須）

公開後、以下を **必ず** 設定してください。設定しないとデータが第三者から読み書き・削除される可能性があります。

### A. Firestore セキュリティルール

1. [Firebase Console](https://console.firebase.google.com/) → プロジェクト → **Firestore Database** → **ルール** タブ
2. 内容を以下に書き換えて **「公開」**：

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /requests/{doc} {
      // 匿名認証済みユーザーのみ許可
      allow read, write: if request.auth != null;
    }
  }
}
```

アプリ起動時に Firebase Anonymous Auth で自動サインインするため、正規ユーザーは影響なし。  
直接 REST API で叩く外部攻撃をブロックします。

### B. Firebase で匿名認証を有効化

1. Firebase Console → 左メニュー **「Authentication」** → **「始める」**
2. **「Sign-in method」** タブ → **「匿名」** を有効化 → 保存

### C. API キーのドメイン制限

1. [Google Cloud Console](https://console.cloud.google.com/) → 該当プロジェクトを選択
2. **「APIとサービス」** → **「認証情報」**
3. ブラウザ用 API キーをクリック
4. **「アプリケーションの制限」** → **「ウェブサイト」** を選択
5. **「ウェブサイトの制限を追加」** で以下を追加：
   - `https://あなたのGitHubユーザー名.github.io/*`
   - 例: `https://ri99-wood.github.io/*`
6. 保存

### D. PIN を変更

`index.html` 内の以下を必ずデフォルトから変更してください：

```js
const PINS = { client: '1234', trainer: '5678' };
```

⚠️ PIN は JS に直書きされているため DevTools で見られます。あくまで **UI 上の利便性のための仕切り** であり、セキュリティの本質は上記 A〜C です。

### E. その他の対策（推奨）

- **Firebase App Check** を有効化（reCAPTCHA v3）すると、正規アプリ以外からのリクエストをブロックできます
- 真の本人認証が必要なら **メール/パスワード認証**（Firebase Auth）に切り替えを検討

---

## ⚠️ XSS対策について

すべてのユーザー入力（メモ・トレーナーメッセージ）は HTML エンティティエスケープしています。  
`<script>` などを入力されても実行されません。
