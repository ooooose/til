# TmuxとvimとNeoVimのインストール(brew経由)
brewをインストールされていることが前提
[HomeBrew公式](https://brew.sh/index_ja)
## zshの設定
以下のコマンドでzshが設定されているか確認する。
```
zsh --version
```
zshがない場合は、以下確認し、zshを設定する。<br />
現在のシェルの確認は以下のコマンドする
```
echo $SHELL
```
設定できるシェルの確認を行う。
```
cat etc/shells
```
zshがあれば以下のコマンドを実行し、zshに設定を変える。
```
chsh -s /bin/zsh
```
## Tmuxのインストール
以下のコマンドを実行し、brew経由でインストールする。
```
brew install tmux
```
バージョンを確認し、返ってきたらインストール成功。
```
tmux -V
```
## Vimのインストール
以下のコマンドを実行し、brew経由でインストールする。
```
brew install vim
```
バージョンを確認し、返ってきたらインストール成功。
```
vim -version
```
## NeoVimのインストール
以下のコマンドを実行し、brew経由でインストールする。
```
brew install neovim
```
バージョンを確認し、返ってきたらインストール成功。
```
nvim -version
```
