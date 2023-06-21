
## キーペア作成

- AWSコンソール > EC2 > キーペア > キーペアを作成
- ダウンロードされた`.pem`ファイルを指定ディレクトリに格納

```
$ mv ~/Downloads/db-access-key.pem ~/.ssh
$ chmod 400 ~/.ssh/db-access-key.pem
```