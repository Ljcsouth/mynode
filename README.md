
# Docker向けのプロジェクト概要
Docker向けの環境構築およびDocker実行環境でアプリ実行を行う。

## 前提条件

 1.Ubuntu22.04以降をインストール済み (AWSのEC2インスタンス、ロカールのいずれも１つ)  
 2.Windows10以降   
 3.ストレージ空き容量：20GB以上  

### 事前準備

ロカールのUbuntuでDockerプロジェクトを構築して、実行できる手順を説明する。  
１.デスクトップで右クリックし、選択肢「ターミナルを開く」をクリックする。  
２.Ubuntuコマンドプロンプトを開く。（デフォルトrootログインされている）  
３．以下のようなコマンドが表示されたら、ログイン成功。
>root@ubuntu:~#  

4.ユーザ作成および、sudo権限付与。作成したユーザに切り替える。  
>adduser user_name  
>gpasswd -a user_name sudo  
>su- user_name

システム更新
>$ sudo apt-get update  
>$ sudo apt-get upgrade  
>$ sudo apt-get autoremove  

システムの依頼パッケージをインストール(Docker公式の署名鍵をシステムに登録する)
>$ sudo apt-get -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

Ubuntuのバージョン情報確認
>$ cat /etc/lsb-release


①Ubuntu22.04以前のバージョン場合  
1.Docker用の情報ライブラリーキーペアを追加  
>$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -  

2.Dockerのキーペア情報をリポジトリに登録する   
>$ sudo add-apt-repository \  
> "deb [arch=amd64] https://download.docker.com/linux/ubuntu \  
> $(lsb_release -cs) \  
> stable"

3.登録済みキーペアであるかを確認  
>$ cat /etc/apt/docker.list|grep docker  


②Ubuntu22.04バージョン以降の場合、以下のインストール方法(現代版)を行う。

1.キーペアを保存するディレクトリを作成(既存の場合、スキップ)  
>$ sudo mkdir -p /etc/apt/keyrings  

2.GPG鍵をダウンロードして指定場所に保存    
>$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  

3.Docker用のキーペア情報をリポジトリに登録する  
>echo \  
> "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \  
> $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  

4.登録済みキーペアであるかを確認  
>$ cat /etc/apt/sources.list.d/docker.list|grep docker

5.当時ユーザをグループに追加    
>sudo usermod -aG docker $USER  

### Dockerインストール  
１．パッケージリストを更新（これで新しい設定が読み込まれます）  
>$ sudo apt-get update  

2.以下のコマンド実行すると、Dockerインストールできる  
>$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

Dockerインストール成功できるかを確認
>$ docker -v  

Docker実行コマンド（デフォルト）  
>＄ sudo docker run hello-world  

実行結果は「Hello from Docker!」が出てましたら、Docke環境構築成功になりました。

## Dockerプロジェクトを新規作成  
githubでmynodeプロジェクトを新規作成(mynode.git)  

ロカールのubuntuへログインして、指定フォルダを作成  
>$ mkdir mydocker  
>$ cd mydocker/

#### githubから作成したプロジェクトをダウンロードする  
※githubのユーザ名とパスワードは求められます。  
>$ git clone https://github.com/Ljcsouth/mynode.git

#### Node.js LTSの鏡像の取得  
>$ sudo docker image pull node:lts  

テスト用のJSを作成  
>$ nano hello.js    

JS内容：  
>console.log("I love this game.")  

Dockerファイルを作成　　
>$ nano Dockerfile

Dockerfile内容：
>FROM node:lts
>
>RUN mkdir /src
>
>COPY hello.js /src
>
>CMD ["node", "/src/hello.js"]

#### ビルド実行およびDocker実行環境へアップロード  
>$ sudo docker image build -t ljcsouth/mynode:v01 .  

パッケージ作成できるかを確認  
>$ sudo docker image ls

実行環境でパッケージとして実行する。  
>$ sudo docker container run ljcsouth/mynode:v01  

実行結果：  
I love this game.  

Dokcer Hubにリソースをプッシュする
>$ sudo docker push ljcsouth/mynode:v01

### 最後  
gitHubへリソースコミット  
>git add.  
>git commit -m "mynode_v01"  

pushするときに、ユーザ名とパスワード(gitHubで発行された臨時用のパスワード)が求められる。  
>git push

Tag作成　　
>git tag v01  
>git push origin v01  






