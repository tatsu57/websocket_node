# node.jsでWebSocket(インストール・導入編)

## 1.node.jsをインストール


#### nvmのインストール

```
git clone https://github.com/creationix/nvm.git ~/.nvm
source ~/.nvm/nvm.sh
```

動作確認。ヘルプがでれば、OK

```nvm help```

#### nvmコマンドでnode.jsをインストール

nvmコマンドでバージョンを確認する

```nvm ls-remote```

お好きなnode.jsのバージョンをインストール

```nvm install 0.12.7```

#### nvmの設定
nvmコマンドをいつでも使える状態にする。これをやらないと毎回、起動しなければいけない
bashやzshの設定に以下を加える。

bash_profileを開く。zshの場合は.zshrc

```vi ~/.bash_profile```

最後の上に以下を追加

```
if [[ -s ~/.nvm/nvm.sh ]];
	then source ~/.nvm/nvm.sh
fi
```

### 最終確認
ターミナルを再起動してみて、以下のコマンドでバージョンが確認できれば、完了。

``` node -v ```

## 2.socket.ioをインストール

好きな場所にフォルダーを作る

```
mkdir フォルダー名
```

socket.ioをインストール

```
npm install socket.io --save-dev
```

グローバルにインストールしたい場合は```--save_dev```を```-g```にする


## 3.サンプルファイルをフォルダーにコピーする

```
githubをclone
```

準備完了

[socket.ioの解説に進む](./comment.md)
