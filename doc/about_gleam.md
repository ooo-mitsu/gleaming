# Gleam言語

## 概要

- イギリスの Louis Pilfold 氏が開発しました。
- 「シンプルさ」「親切さ」「安全さ」を重視して設計された静的型付けの関数型プログラミング言語です。
  - シンプルさ
    - 馴染みやすい構文
      - 関数型ですが、JavaScript,Rust,Goなどの見慣れたモダンな構文を採用しています。
    - 小さな言語仕様
      - if 文や for ループがありません。代わりにパターンマッチン(case式)や再帰を使って記述します。

  - 親切さ
    - 親切なコンパイラ
      - Rustに影響を受けた親切なエラーメッセージを出力します。
  - 安全さ
    - 静的型システムとコンパイル時の強力な分析で実行時の予期せぬクラッシュを最小限に抑えてくれます。
    - Nullや例外はなく、Option型やResult型を用いて値の不在や失敗を明示的に扱い、実行時のクラッシュを防ぎます。
- Erlang仮想マシン(BEAM)およびJavaScriptランタイムで動作します。
  - 同一の言語でバックエンドからフロントエンド(ブラウザ,Node.jsなど)まで開発でき、コードの共有も可能です

## インストール

[Installing Gleam](https://gleam.run/install/)に、各OS のインストール方法が記載されています。

私は、WSL2(Ubuntu)で、まずは、erlang-base のインストールを行いました。

```bash
sudo apt install erlang-base
```

そのあとに、gleam をインストールしました。

```bash
wget https://github.com/gleam-lang/gleam/releases/download/v1.16.0/gleam-v1.16.0-x86_64-unknown-linux-musl.tar.gz
wget https://github.com/gleam-lang/gleam/releases/download/v1.16.0/gleam-v1.16.0-x86_64-unknown-linux-musl.tar.gz.sha512
sha512sum -c gleam-v1.16.0-x86_64-unknown-linux-musl.tar.gz.sha512
tar xvf gleam-v1.16.0-x86_64-unknown-linux-musl.tar.gz
chmod 755 gleam
mkdir ~/.local/bin
mv gleam ~/.local/bin/
source ~/.bashrc
gleam --version
```

PATH が通っていない場合は、`~/.bashrc` に下記を追加する必要があります。
```bash
export PATH=$PATH:$HOME/.local/bin
```

`~/.bashrc`追記後に、`source ~/.bashrc` で、gleam が実行できるようになっているハズです。

また、VS Code の拡張機能「Gleam」もインストールしました。

## Hello Wrold.

以下の手順で実行しました。

- プロジェクトの作成: `gleam new hello`
  - ディレクトリ構造
    ```
    $ tree hello
    hello
    ├── README.md
    ├── gleam.toml
    ├── manifest.toml
    ├── src
    │   └── hello.gleam
    └── test
        └── hello_test.gleam
    
    3 directories, 5 files
    ```
- カレントディレクトリ移動: `cd hello`
- ソースコード(`gleam new hello`で自動生成されます): `$ cat src/hello.gleam`
  ```gleam
  import gleam/io
  
  pub fn main() -> Nil {
    io.println("Hello from hello!")
  }
  ```
- 実行(erlang): `gleam run`
  ```bash
  $ gleam run
  Downloading packages
   Downloaded 2 packages in 0.01s
    Compiling gleam_stdlib
    Compiling gleeunit
    Compiling hello
     Compiled in 0.48s
      Running hello.main
  Hello from hello!
  ```

- 実行(javascript): `gleam run --target javascript`
  ```bash
  $ gleam run --target javascript
  Downloading packages
   Downloaded 2 packages in 0.01s
    Compiling gleam_stdlib
    Compiling gleeunit
    Compiling hello
     Compiled in 0.08s
      Running hello.main
  Hello from hello!
  ```
