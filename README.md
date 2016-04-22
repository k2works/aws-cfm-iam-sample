# 目的
AWS CloudFormationでIAMユーザーを管理できるようにする

# 前提
| ソフトウェア     | バージョン    | 備考         |
|:---------------|:-------------|:------------|
| docker         | 1.10.3       |             |
| docker-compose | 1.6.2        |             |
| vagrant        | 1.7.4        |             |
| aws-cli        | 1.10.17      |             |
| EB CLI         | 3.7.4        |             |

+ AWSのアカウントを作っている

# 構成

+ 準備
+ ハンズオン
+ 片付け

# 詳細
## 準備
```
$ vagrant up
$ vagrant ssh
$ cd /vagrant/
$ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXXXXXXX
AWS Secret Access Key [None]: XXXXXXXXXXXXXXXXXX
Default region name [None]: ap-northeast-1
Default output format [None]: json
```

## ハンズオン
### ユーザーを作成する
```
$ aws cloudformation validate-template --template-body file://iam-1user.template
{
    "CapabilitiesReason": "The following resource(s) require capabilities: [AWS::IAM::AccessKey, AWS::IAM::User]", 
    "Description": "AWS CloudFormation Sample Template.", 
    "Parameters": [
        {
            "NoEcho": true, 
            "Description": "New account password", 
            "ParameterKey": "Password"
        }
    ], 
    "Capabilities": [
        "CAPABILITY_IAM"
    ]
}
$ aws cloudformation create-stack --stack-name Sample --template-body file://iam-1user.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
{
    "StackId": "arn:aws:cloudformation:ap-northeast-1:262470114399:stack/Sample/6d1de260-085b-11e6-a1c5-50a68a175ae6"
}
```
### グループを作成する
```
$ aws cloudformation validate-template --template-body file://iam-1group-1user.template
$ aws cloudformation update-stack --stack-name Sample --template-body file://iam-1group-1user.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
{
    "StackId": "arn:aws:cloudformation:ap-northeast-1:262470114399:stack/Sample/d3aec030-085b-11e6-a229-500c596c228e"
}
```
### ポリシーをグループに割り当てる
```
$ aws cloudformation validate-template --template-body file://iam-1group-1user-1policy.template
$ aws cloudformation update-stack --stack-name Sample --template-body file://iam-1group-1user-1policy.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
```
### ユーザーを追加する
```
$ aws cloudformation validate-template --template-body file://iam-1group-2user.template
$ aws cloudformation update-stack --stack-name Sample --template-body file://iam-1group-2user.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
```
### グループを追加する
```
$ aws cloudformation validate-template --template-body file://iam-2group-2user.template
$ aws cloudformation update-stack --stack-name Sample --template-body file://iam-2group-2user.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
```
### 追加したグループにポリシーを割り当てる
```
$ aws cloudformation validate-template --template-body file://iam-2group-2user-2policy.template
$ aws cloudformation update-stack --stack-name Sample --template-body file://iam-2group-2user-2policy.template --parameters ParameterKey=Password,ParameterValue=sample12345 --capabilities CAPABILITY_IAM
```
## 片付け
```
$ aws cloudformation delete-stack --stack-name Sample
$ exit
$ vagrant destroy
```

# 参照
+ [AWS CloudFormatin](http://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/best-practices.html)
+ [IAMとは](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/introduction.html)
+ [サンプルテンプレート](http://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/sample-templates-services-ap-northeast-1.html#d0e119371)
+ [AWS CLIでサービスの各種コマンドを動かしてみる(CloudFormation編)](http://dev.classmethod.jp/cloud/aws/awscli-cloudformation/)
+ [SNS/SQSで始めるCloudFormation](http://dev.classmethod.jp/etc/cfn_with_sqs_sns/)
+ [AWS リソースプロパティタイプのリファレンス](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
+ [AWS CLI CloudFormation](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/)

