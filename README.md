# 初期設定

## STEP:1 キーペア作成

- AWSコンソール > EC2 > キーペア > キーペアを作成
- ダウンロードされた`.pem`ファイルを指定ディレクトリに格納

```shell
$ mv ~/Downloads/access-key.pem ~/.ssh
$ chmod 400 ~/.ssh/access-key.pem
```

## STEP:2 パラメーターストアにAWS Cloud Watch設定ファイルをアップロード

cloudformationのスタックをアップロードする前に実施してください。

```shell
$ aws ssm put-parameter --name "/config/cw-agent" --type "String" --value "$(cat cw-agent.json)"
```

## STEP:3 Cloud Formationのスタックをアップロード

AWSコンソール > CloudFormation > スタック > スタックの作成

## STEP:4 Cloud Watch Agentのインストール

```shell
$ ssh -i ~/.ssh/access-key.pem ec2-user@<EC2のIPアドレスまたはホスト名>
$ sudo yum install -y amazon-cloudwatch-agent
$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/config/cw-agent -s
```
