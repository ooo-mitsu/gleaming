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

## コメント

| 種類                                   | 記法   | 用途                                                                     |
| :------------------------------------- | :----- | :----------------------------------------------------------------------- |
| **通常のコメント**                     | `//`   | 一般的なコードの説明やメモ用                                             |
| **ドキュメントコメント(アイテム用)**   | `///`  | 型や関数の定義の直前に記述し、そのアイテムのドキュメントとして機能します |
| **ドキュメントコメント(モジュール用)** | `////` | モジュールの先頭に記述し、モジュール全体の説明に使用されます             |

### コメントのルール

- **行コメントのみ:** 複数行コメント(ブロックコメント)の構文はありません。
- **配置場所:** コメントは、対象とするコードの**前の行**に記述します。コードと同じ行の末尾(行末コメント)に記述した場合、コードフォーマッタによって自動的に次の行へ移動されます。
- **ドキュメント生成:** `///` や `////` で記述された内容は、ツールを使用してドキュメントを生成する際に反映されます。

```gleam
//// モジュールの説明

/// typeの説明
pub type Never {
  Never(Never)
}

// 通常のコメント

/// fnの説明
pub fn twice(argument: value, my_function: fn(value) -> value) -> value {
  // 通常のコメント
  my_function(my_function(argument))    // 通常のコメント(コードフォーマッタを通すと下行へ移動してしまうので注意)
}
```

## 型

| 分類             | 型名             | 概要                                                     | 例                                |
| :--------------- | :--------------- | :------------------------------------------------------- | :-------------------------------- |
| **基本型**       | **Int**          | 整数型。アンダースコアでの区切りや、16進数等の表記も可能 | `1`, `-5`, `1_000_000`, `0x1A`    |
|                  | **Float**        | 浮動小数点数型。指数表記もサポート                       | `3.14`, `1.0`, `6.022e23`         |
|                  | **String**       | UTF-8エンコードされた文字列。複数行も可能                | `"Hello"`, `"Gleam⭐"`           |
|                  | **Bool**         | 真偽値                                                   | `True`, `False`                   |
|                  | **Nil**          | 「値がない」ことを表すユニット型 (Nullとは異なる)        | `Nil`                             |
| **コレクション** | **List**         | 同一型の値を保持する連結リスト                           | `["a", "b"]`                      |
|                  | **Tuple**        | 異なる型を混在できる固定サイズの集合                     | `#(1, "hi")`, `#(3.14, True)`     |
|                  | **Dict(k, v)**   | キーと値のペア。標準ライブラリ(gleam/dict)を使用して作成 | `dict.from_list([#("a", 1)])`     |
|                  | **BitArray**     | バイナリデータ(ビットの並び)                             | `<<255, 0, 100>>`, `<<"A":utf8>>` |
| **特殊型**       | **Result(a, e)** | 成功(Ok)または失敗(Error)を表す型                        | `Ok(200)`, `Error("Not Found")`   |
|                  | **Option(a)**    | 値の存在(Some)または不在(None)を表す型                   | `Some(42)`, `None`                |
|                  | **Dynamic**      | 外部から入る型不明のデータ。デコードして使用             | `dynamic.from(123)`               |
| **ユーザー定義** | **Custom Type**  | `type`キーワードで定義する独自の列挙型                   | `type Status { Online Offline }`  |
|                  | **Record**       | カスタム型のバリアントにデータを持たせたもの             | `User(id: 1, name: "Joe")`        |

### 型の補足

- **厳格な区別:** `Int`と`Float`は厳密に区別されます。例えば、整数には`+`、浮動小数点数には`+.`というように、使用する演算子も異なります。
- **イミュータブル（不変）:** 上表にあるすべてのデータ型は不変です。一度作成した値を書き換えることはできず、変更が必要な場合は新しい値を作成(あるいはレコードアップデート構文を使用)します。
- **型推論:** Gleamは強力な型推論を持つため、多くの場合で`let x = 1`のように型を省略できますが、`let x: Int = 1`のように明示的に注釈を付けることも可能です。
- **カスタム型の柔軟性:** カスタム型は単一のバリアント(構造体のような使い方)から、複数のバリアント(列挙型のような使い方)まで幅広く定義でき、パターンマッチングと組み合わせて強力な制御フローを構築できます。
  - 複数バリアント(列挙型)
    ```gleam
    pub type Status {
      Online
      Offline
      Away
    }
    ```
  - 複数バリアント(レコード, 共用体的)
    ```gleam
    pub type SchoolPerson {
      Teacher(name: String, subject: String)
      Student(name: String, age: Int)
    }
    ```
  - 単一バリアント(構造体的)
    ```gleam
    pub type User {
      User(id: Int, name: String, email: String)
    }
    ```
