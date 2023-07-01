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

AWS Systems Manager > パラメータストア > `/config/cw-agent` > 概要

設定ファイルが格納されていることを確認します。

![スクリーンショット 2023-07-01 11 10 51](https://github.com/WebEngrChild/cw-ec2-nginx/assets/87892265/5710945a-d1cf-41f6-a54a-b63d43933df1)


## STEP:3 Cloud Formationのスタックをアップロード
```shell
$ aws cloudformation create-stack \
    --stack-name cw-ec2-nginx \
    --template-body file://cfn.yml \
    --parameters ParameterKey=KeyName,ParameterValue=access-key ParameterKey=SourceIpAddress,ParameterValue="0.0.0.0/0" \
    --capabilities CAPABILITY_IAM
```

最後のIPアドレスの箇所は適宜ご自身の環境のグローバルIPを指定してください。

## STEP:4 Cloud Watch Agentのインストール

マネージメントコンソール > EC2 > > IPアドレスをコピー

```shell
$ ssh -i ~/.ssh/access-key.pem ec2-user@<上記で取得したEC2のIPアドレス>
$ sudo yum install -y amazon-cloudwatch-agent
$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/config/cw-agent -s
```

## STEP:5 スタック削除

```shell
$ aws cloudformation delete-stack --stack-name cw-ec2-nginx
```
