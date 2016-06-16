# Section 6 AWS

## 6-0 AWSコマンドラインインターフェイスのインストール
[公式サイト](http://docs.aws.amazon.com/ja_jp/cli/latest/userguide/installing.html)参照。

[pypa.io](https://pip.pypa.io/en/latest/installing/)からpythonのスクリプトをダウンロード/インストール

'$ curl -O https://bootstrap.pypa.io/get-pip.py'

'$ sudo python get-pip.py'

Pythonのスクリプトを実行してAWS CLIをインストール

'$ sudo pip install awscli'

AWS CLIの設定
'aws configure
AWS Access Key ID [None]: ********
AWS Secret Access Key [None]: ********
Default region name [None]: ap-northwest-1
Default output format [None]: json
'

## 6-1 AWS EC2+Ansible

Ansibleで構築するやり方
Ansibleの作業ディレクトリに移動して
'$ scp -r * 学籍番号@172.16.40.2
$ ssh 学籍番号@172.16.40.2
'
sshでアクセスしたらscpでコピーしたplaybook.ymlのhostsのIPアドレスを更新する。
hostファイルのIPアドレスも書き換える。

'ansible-playbook playbook.yml -i hosts  --private-key s14002.pem -u ec2-user -k'

パブリックDNSをコピーしてhttp://パブリックDNSをペーストしてWordpressにアクセスする

### AMI(Amazon Machine Image)を作る
AWSにログインしてEC2のAMIで作成ボタンをクリックする。イメージを作成。
インスタンスの作成でマイAMIを選択、作成したAMIを選択。
同様の方法でインスタンスを作成する。
パブリックDNSでhttp://にアクセスしてWordpressを確認する。

## 6-2 AWS EC2(AMIMOTO)
EC2からインスタンスを作成をクリックしてAWS Marketplaceをクリックする。
検索するところにAMIMOTOと入力する。WordPress powered by AMIMOTO(HVM)を選択
インスタンスを選択、「確認して起動」をクリック 起動をクリック
自分のキーペアを設定
インスタンスを起動する、左メニューのエラスティックIPを選択して、新規のアドレスを割り当てるをクリック
EIPとインスタンスの紐付けのため、アドレスの関連付けをクリックする。
instanceで自分のインスタンスIDを入力する。
インスタンスに移動してパブリックDNSをコピーしてhttp://のあとに入力する。
自分のインスタンスIDを入力してWordpressを確認する。

## 6-3 S3
S3に移動する。
ファイル(ローカルに適当にtest.htmlを作成)のアップロード
'$aws s3 cp test.html s3://s14002/'
'$aws aws s3 chmod 755 s3://s14002/test.html'
'aws s3 cp --acl public-read test.html s3://s14002'
## 6-4 CloudFront
## 6-5 ELB
## 6-6 AWS Lambda
