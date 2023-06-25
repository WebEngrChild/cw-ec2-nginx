# 初期設定

## キーペア作成

- AWSコンソール > EC2 > キーペア > キーペアを作成
- ダウンロードされた`.pem`ファイルを指定ディレクトリに格納

```shell
$ mv ~/Downloads/access-key.pem ~/.ssh
$ chmod 400 ~/.ssh/access-key.pem
```

## EC2にssh接続

```shell
ssh -i ~/.ssh/access-key.pem ec2-user@<EC2のIPアドレスまたはホスト名>
```

## パラメーターストアにAWS Cloud Watch設定ファイルをアップロード

- cloudformationのスタックをアップロードする前に実施すること

```shell
$ aws ssm put-parameter --name "/config/cw-agent" --type "String" --value "$(cat cw-agent.json)"
```

## Cloud Formationのスタックをアップロード

