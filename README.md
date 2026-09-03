# kitte_manage

切手・収入印紙・葉書などの在庫管理と、不足分の請求書画像を生成するシングルページアプリ（`index.html` 単体）です。
データの保存先は Firebase（Firestore）です。

## Firebaseのセットアップ

1. [Firebase コンソール](https://console.firebase.google.com/)で新規プロジェクトを作成する。
2. 「Firestore Database」を作成する（本番モードでもテストモードでも可。認証なし運用のためルールは後述の内容にする）。
3. プロジェクトの設定 > 全般 > マイアプリ で「ウェブアプリを追加」し、表示された `firebaseConfig` の値をコピーする。
4. `index.html` 内の `firebaseConfig`（`YOUR_API_KEY` などのプレースホルダー）を、コピーした値に書き換える。
5. Firestore の「ルール」タブで以下を設定して公開する（認証なしで読み書きする運用のため）。

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /kitteManage/{docId} {
      allow read, write: if true;
    }
  }
}
```

※誰でも読み書きできる設定です。第三者にURLやFirebase設定値が知られないよう取り扱いに注意してください。

## 旧スプレッドシート(GAS)データの移行

以前の Google Apps Script 連携版からの移行用に、「⚙️ マスタ管理」タブに
「📥 旧スプレッドシート(GAS)のデータをFirebaseへ移行」ボタンがあります。
Firebase設定を済ませた状態でこのボタンを押すと、旧GASのWebアプリURLからデータを取得し、
Firestoreへ書き込みます（Firestore側の既存データは上書きされます）。移行が完了したら
`index.html` 内の `OLD_GAS_API_URL` や旧GASプロジェクトは削除して構いません。
