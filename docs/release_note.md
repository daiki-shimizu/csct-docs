V2.0.3
------
以下の機能を追加しました。

- SDSFのDAパネル(Display Active users)を使って、稼働しているユーザー名一覧を取得するSDSF.active_usersメソッドを追加しました。
- 稼働しているユーザーのジョブログを取得するSDSF.active_user_joblogメソッドを追加しました。
 

V2.0.2
------
以下の修正を行いました。

- csct_create_sampleを実行した直後など、log.txtが存在しないときに、csct_display_logとcsct_notify_slackでエラーになる問題を修正しました。
 

V2.0.1
------
問題判別に役立つプログラムを２つ追加しました。

 

- csct_display_log (directory_path)
directory pathに関連するZOS_env.jsonの内容と、最後に実行したときにログに記録されたメッセージを表示します。
- csct_notify_slack (directory_path)
directory pathに関連するZOS_env.jsonの内容と、最後に実行したときにログに記録されたエラーメッセージをSlackに送信します。
このプログラムを使うときは、あらかじめSlackのincoming webhookのURLを入手し、ZOS_env.jsonファイルのslack_incoming_webhook_urlに設定しておく必要があります。
セキュリティ上の理由により、ZOS_env.jsonに記述されているパスワードは'XXXXXX'で置き換えられます。
 


ZOS_env.jsonの例

```json
{
  "G3": {
    "ipaddress": "xxx.xxx.xxx.xxx",
    "userid": "xxxxx",
...
    "slack_incoming_webhook_url": "https://hooks.slack.com/services/xxxxxx",
    "slack_incoming_webhook": {
      "username": "your name",

      "channel": "#incoming_webhook"
    }
  }
}
```

 

V2.0（2018/04/27 release)
------
V2.0をリリースしました。Filesのセクションにあるgemファイルをダウンロードして、お使いください。

 

 

V2.0（2018/03/05 preview)
------
2018年4月にリリースする予定です。このV2.0は、使い勝手を大幅に引き上げ、初めての方でもすぐに使えることを目指した戦略的なバージョンになります。将来的には、リファレンスガイドの提供、Webインターフェースの実装、z/OSMF対応、Pythonからの呼び出しなど、さまざまな機能拡張を予定していますので、これから現行値取得ツールを使う方は、V2.0をお使いください。

 

なお、V2.0をリリースしたタイミングで、既存のV1.xはメンテナンスリリースとして、メンテナンスのみを行います。すでにV1.xをお使いの方は、V2.0への移行を検討ください。

 

新機能
- Rubyを導入した後にgem install コマンドを実行するだけで、現行値取得ツールを導入することができるようになりました。従来のようにzipファイルを展開したり、展開先のディレクトリを意識したりする必要はなくなりました。
- 現行値取得ツールを呼び出すRubyプログラムの先頭に、従来は7行ほどのおまじないを書く必要がありましたが、バージョン2.0では、以下の3行を記述するだけでよくなりました。これだけで、自動的にZOS_env.jsonファイルを読み込み、引数(ARGV[0])で指定したdirectory pathを使うようになります。


```ruby
require 'zos_standard_library'
include ZOS
Config.directory_path = ARGV[0]
```
 

- ZOS_env.jsonファイルの記述がシンプルになりました。括弧がすべて中括弧( { } )になり、ZOS_FTPやZOS_Functionなどの接頭語がなくなりました。また、これまで指定できなかったパラメータ（例 MSGCLASSなど）をこのファイルで指定することができるようになりました。

```json
{
  "ZOS_SSH": {
    "ipaddress": "9.188.216.194",
    "userid": "SHIMIZU",
    "connection_type": "SSH",
    "msgclass": "H",
    "ssh_port": 222,
    "ssh_key": "id_rsa"
  }
}
```
 

- vtoclist.jcl.erbを廃止し、dslist.jcl.erbを使うようにしました。これにより、データセット名にワイルドカードを使うことができるようになりました。使用方法はこれまでと変わりません。
ACS Routineのソースコードが格納されているデータセット名を取得できるようにしました。
- TCPIPスタックでIPv6を定義するなど、NETSTATの形式が通常のshort formatではなく、long formatになっている場合があります。そのときでも、正しくTCPポート番号を取得することができるようにしました。
 

V1.xからV2.0への移行時の考慮点
- syscmd_methodにおいて、最も信頼性の高い方法であるISFEXECを使うように一本しました。これに伴い、GETMSGやULOGは廃止しました。
- ISPFバッチを動かすためのDD名をカスタマイズしたい場合、ZOS_env.jsonファイルで指定することができるようになりました。これまで、ISPFバッチの雛形のJCLを修正していた方は、ZOS_env.jsonファイルを修正するようにしてください。
 

V1.3
------
新機能
- D NET,MAJNODESやD NET,VTAMOPTSなど、実行した結果を取得できないMVSシステムコマンドがいくつかありました。これを改善するため、ISFEXECというsyscmd_methodを追加しました。
 

 

V1.2.1
------
新機能
- これまで、データセットに関する様々な定義情報を取得することができましたが、データセットが最後に参照された参照日や有効期限を取得することができませんでした。そこで、参照日と有効期限を一緒に取得する新しいJCL(dslist.jcl.erb)を提供しました。
 

V1.2
------
新機能
- SSHでz/OSに接続することができるようになりました。SSHのポートフォワーディングも利用可能です。ZOS_env.jsonで以下の設定を行ったz/OSに対しては、FTPではなく、SSHで接続します。この機能を使う場合は、あらかじめnet-sshとnet-scpというRubyのライブラリをPC上に導入しておく必要があります。


```ruby
ZOS_FTP.connection_type = 'SSH'
ZOS_function.syscmd_method = 'GETMSG'
```

- ジョブログを取得する方法を指定するパラメータ(ZOS_function.syscmd_method)の挙動を確認するsyscmd_test.rbプログラムを機能拡張しました。デフォルトでは、これまで通り、'GETMSG'および'ULOG'を指定したときのそれぞれの実行結果を表示しますが、ZOS_env.jsonにてこのパラメータが明示的に指定されているときは、その値での実行結果のみを表示するようになりました。
- ZOS_env.jsonのZOS_FTP.ftp_portにて、FTPで利用するポート番号を変更することができるようになりました。
- デバイスアドレスの一覧から、"開始するデバイスアドレス,アドレス数"に変換するmultiple_address_with_countメソッドを実装しました。MVSシステムコマンドである"D U,DASD,,"では、ステータス表示させるデバイスアドレスを"開始するデバイスアドレス,アドレス数"という形式で指定します。このメソッドは、デバイスアドレスの一覧から、このコマンドに合わせた形式に変換する際に利用することができます。
 

 

V1.1 
------
新機能
- Windowsに加えて、Linux、Mac OSに対応しました。
エラーハンドリング処理を強化し、ツールの堅牢性を大幅に高めました。
- SDSF ULOGに加えて、Rexx GETMSGでジョブログを取得する方法を選択できるようにしました。お使いのz/OS環境では、どちらの方法が適切かを事前に判断できるようにするツール(syscmd_test.rb)を同梱しました。
- 複数のz/OSの現行値を取得する場合に備え、Excel_write_Pf.rb / Excel_write_Pfm.rbを実行する際に、directory pathを引数に指定するように仕様を変更しました。合わせて、作成したnew_form Excelファイルを、指定したdirectory path以下に格納するように変更しました。これにより、複数のz/OSに対してツールを実行する際に、逐一、new_form Excelファイルを退避する必要がなくなりました。
- 導入したらすぐに稼動確認が行えるように、サンプル定義をツールに同梱しました。
- システム・シンボルに対応しました。システム・シンボルのサブストリングも解釈することができます。
- PARMLIBデータセット内のメンバーを検索する機能を実装しました。これにより、ANTMIN00など、IEASYSxxなどからたどることができないメンバーも見つけることができるようになりました。
- SYSLOGを準備しなくても実行できるように、検索機能を強化しました。
- Windowsのメモ帳を使ってファイルを編集すると、ファイルにBOM（Byte Order Mark）が付与されます。ZOS_env.jsonにBOMが付与されていても、エラーにならないように対処しました。
- SDSF ULOGを使ってジョブを実行する際に表示されるコンソール名を、ZOS_env.jsonで変更できるようにしました。
- Rubyの稼動環境をまとめて１つの実行ファイルにまとめてくれるocraというツールに対応しました。
 

制約
- ツールを導入したディレクトリのパス名にダブルバイト文字が入っていると実行することができません。シングルバイト文字に置き換えてから実行してください。
- RMF、TN3270、VTAMのスタートプロシージャー名がRMF、TN3270、VTAM以外の名前になっている環境では、正しい現行値を取得することができません。これは将来のリリースで修正する予定です。
- システムシンボルの値として利用できるのは、DISPLAY SYMBOLSコマンドで表示された値、およびJCL内で指定された値だけです。スタートプロシージャーを開始する際に、スタートオプションでシステムシンボルの値を指定することができますが、この方法で指定した値は無視されます。
 

 

V1.0
------
初版公開