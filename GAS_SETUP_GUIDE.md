# Google Apps Script Webアプリの設定方法

このドキュメントでは、Google Apps Script (GAS) をWebアプリとして公開し、FastAPIからアクセスする方法を説明します。

## 1. GASプロジェクトの確認

プロジェクトの `gas` ディレクトリには、以下のファイルがあります：

- `Code.gs`: メインスクリプトファイル（APIエンドポイント）
- `SpreadsheetService.gs`: スプレッドシートのデータを操作するサービスクラス
- `appsscript.json`: GASプロジェクトの設定ファイル

## 2. GASプロジェクトをGoogle Apps Scriptエディタで開く

1. Googleドライブにアクセス
2. 「新規作成」→「その他」→「Google Apps Script」を選択
3. 新しいプロジェクトが作成されるので、名前を「Rocket2025-UserRecords」などに変更
4. デフォルトの `Code.gs` ファイルが作成されるので、その内容をプロジェクトの `gas/Code.gs` の内容で置き換え
5. 「ファイル」→「新規作成」→「スクリプトファイル」を選択し、`SpreadsheetService.gs` という名前でファイルを作成
6. その内容をプロジェクトの `gas/SpreadsheetService.gs` の内容で置き換え

## 3. プロジェクト設定を行う

1. GASエディタで「プロジェクトの設定」アイコン（歯車マーク）をクリック
2. 「スクリプトのプロパティ」タブを選択
3. 「行を追加」をクリック
4. プロパティに「SPREADSHEET_ID」と入力
5. 値は空欄のまま（初回実行時に自動的に新しいスプレッドシートが作成されます）
6. 「保存」をクリック

## 4. ウェブアプリとして公開

1. GASエディタの右上にある「デプロイ」ボタンをクリック
2. 「新しいデプロイ」を選択
3. 「種類の選択」で「ウェブアプリ」を選択
4. 次の設定を行います：
   - 説明：「Rocket2025 User Records API」
   - 次のユーザーとして実行：「自分」
   - アクセスできるユーザー：「全員」
5. 「デプロイ」ボタンをクリック
6. 初回デプロイ時、アクセス権限の承認を求められるので、「承認」→「アカウントを選択」→「詳細」→「安全ではないページに移動」→「許可」をクリック

## 5. デプロイURLの取得と設定

1. デプロイが完了すると、「ウェブアプリのURL」が表示されます
2. このURLをコピー
3. プロジェクトのルートディレクトリにある `.env` ファイルを開く
4. `GAS_WEBAPP_URL=` の後にコピーしたURLを貼り付け
5. `.env` ファイルを保存

## 6. スプレッドシートへのアクセス

1. GASスクリプトを初めて実行すると、新しいスプレッドシートが自動的に作成されます
2. ログコンソールに新しいスプレッドシートのIDとURLが表示されます
3. 表示されたURLをクリックすると、作成されたスプレッドシートにアクセスできます
4. あるいは、手動でスプレッドシートを作成し、そのIDを「SPREADSHEET_ID」スクリプトプロパティに設定することもできます

## 7. GASスクリプトのテスト

1. GASエディタで、関数として `doGet()` を選択し、「実行」ボタンをクリック
2. 初回実行時は権限の承認が必要なので、指示に従って承認手続きを完了
3. 実行が成功すると、ログ出力に「スクリプトプロパティからスプレッドシートID「...」を読み込みました。」または「スプレッドシートIDがスクリプトプロパティに設定されていません。」というメッセージが表示されます
4. 後者の場合は、新しいスプレッドシートが作成され、そのIDがスクリプトプロパティに自動的に保存されます

## 8. APIのエンドポイント

GAS WebアプリのAPIエンドポイントは、次のような形式で呼び出せます：

### GETリクエスト
- すべてのレコードを取得: `{WebアプリのURL}?action=getAll`
- IDでレコードを検索: `{WebアプリのURL}?action=findById&id=1`
- アカウント名でレコードを検索: `{WebアプリのURL}?action=findByAccount&account=user1`

### POSTリクエスト
- 新規レコード作成: 
  ```
  POST {WebアプリのURL}
  Content-Type: application/json
  
  {"action": "create", "account": "new_user", "score": 100}
  ```

- スコア更新:
  ```
  POST {WebアプリのURL}
  Content-Type: application/json
  
  {"action": "update", "identifier": "user1", "score": 200, "createIfNotExist": true}
  ```

## 9. トラブルシューティング

1. **「アクセス権限が必要です」エラー**
   - スクリプトの実行権限を再度承認してください
   - 「スクリプトのプロパティ」でスプレッドシートIDが正しく設定されているか確認

2. **「このアプリは確認されていません」警告**
   - 「詳細」→「安全ではないページに移動」→「許可」の順にクリックして進んでください

3. **CORS関連のエラー**
   - `appsscript.json` ファイルで `webapp.access` が `ANYONE` に設定されていることを確認
   - デプロイ設定で「アクセスできるユーザー」が「全員」になっているか確認

4. **デプロイされたアプリのURLが変わる場合**
   - 新しいデプロイを作成するたびにURLが変わることがあります
   - 最新のURLを `.env` ファイルの `GAS_WEBAPP_URL` に更新してください
