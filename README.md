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
```shell
$ aws cloudformation create-stack \
    --stack-name cw-ec2-nginx \
    --template-body file://cfn.yml \
    --parameters ParameterKey=KeyName,ParameterValue=access-key \
    --capabilities CAPABILITY_IAM
```

## STEP:4 Cloud Watch Agentのインストール

```shell
$ ssh -i ~/.ssh/access-key.pem ec2-user@<EC2のIPアドレスまたはホスト名>
$ sudo yum install -y amazon-cloudwatch-agent
$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/config/cw-agent -s
```

## STEP:5 スタック削除

```shell
$ aws cloudformation delete-stack --stack-name cw-ec2-nginx
```
