# Beginning Java With CLI: A Practical Introduction



[Beginning Java With CLI](https://github.com/nogayama/beginning-java-with-cli) は、 Java 言語でコマンドラインツールを作りながら、実践的な Java について学ぶチュートリアルです。Mac を前提にしています。



**目次**
[TOC]

# 1. Java環境をセットアップ



## 1.1. セットアップ(システム環境)



1. **Brewをインストール**

    1. 管理者権限があるユーザーとしてログイン
    2. brew をインストール
        ```sh
        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
        ```

    

2. **Javaをインストール**

    1. 管理者権限があるユーザーとしてログイン
    1. `openjdk`をインストール
    ```sh
    brew install openjdk
    ```



## 1.2. セットアップ(ユーザー環境)



1. **環境変数PATHにBrewを追加する**

    1. 普段利用するユーザーとしてログイン

    2. 起動スクリプトに環境変数の宣言を追加する

        ```sh
        echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
        ```

    

1. **環境変数JAVA_HOMEにjavaのフォルダを追加**

    1. 普段利用するユーザーとしてログイン
    2. 環境変数 JAVA_HOME を確認
        ```sh
        echo $JAVA_HOME
        ```
    3. Javaのフォルダが環境変数の中になければ、起動スクリプトに環境変数の宣言を追加する
        ```sh
        echo 'export JAVA_HOME="/opt/homebrew/opt/openjdk"' >> ~/.zshrc
        ```

    

2. **環境変数PATHにjavaコマンドが存在するフォルダを追加**

    1. 普段利用するユーザーとしてログイン
    2. 環境変数 JAVA_HOME を確認
        ```sh
        echo $PATH | grep openjdk
        ```
    3. Java のパスがなければ、起動スクリプトに環境変数の宣言を追加する
        ```sh
        echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc
        ```



3. **動作確認**
   
    1. 普段利用するユーザーとしてログイン
    2. Javaのバージョンを表示
        ```sh
        java --version
        ```
        結果
        ```
        openjdk 25.0.1 2025-10-21
        OpenJDK Runtime Environment Homebrew (build 25.0.1)
        OpenJDK 64-Bit Server VM Homebrew (build 25.0.1, mixed mode, sharing)
        ```
    3. Javac のバージョンを表示
        ```sh
        javac --version
        ```
        結果
        ```
        javac 25.0.1
        ```



# 2. Hello Java コマンドを作成



## 2.1. フォルダ階層の作成からコンパイルまで




1. **フォルダ階層を作る**

    以下のコマンドを実行しフォルダ階層とJavaファイルを作る
    ```sh
    mkdir -p c02_hello_java/bin
    mkdir -p c02_hello_java/src/mypkg
    touch c02_hello_java/src/mypkg/HelloJava.java
    ```

    

    以下のような階層ができる

    ```
    c02_hello_java
    ├── bin
    └── src
        └── mypkg
            └── HelloJava.java
    ```

    

2. **Javaコードを書く**

    `src/mypkg/HelloJava.java`を編集する

    ```java
    package mypkg;
    
    public class HelloJava {
    	public static void main(String[] args) {
    		System.out.println("Hello Java");
    	}
    }
    ```



3. **コンパイルする**
    クラスファイルの出力先として、`bin`フォルダを指定する。

    ```sh
    cd c02_hello_java
    javac src/**/*.java -d bin 
    ```
    
    以下のような階層ができる
    
    ```sh
    ├── bin
    │   └── mypkg
    │       └── HelloJava.class
    └── src
        └── mypkg
            └── HelloJava.java
    ```



## 2.2. Java アプリを実行する



4通りの方法で実行してみる

1. **1行で実行する**

    ```sh
    java -classpath bin mypkg.HelloJava
    ```
    結果
    ```
    Hello world
    ```




5. **クラスパスを環境変数に指定して実行する**

    ```sh
    export CLASSPATH=bin
    java mypkg.HelloJava
    ```



6. **シェルスクリプトから実行する**
   
    1. シェルスクリプトフォルダ`sh`を作る
        ```sh
        mkdir sh
        touch sh/hellojava
        ```
        
    1. テキストエディタで `sh/hellojava` を編集する
       
        ```sh
        #!/usr/bin/env bash
        
        # このシェルスクリプトが存在しているフォルダの絶対パス
        THIS_DIR="$(cd $(dirname $0);pwd)" 
        
       # 一つ上を経由したbinフォルダのパスをクラスパスに追加
       export CLASSPATH="${THIS_DIR}/../bin" 
       
       # このシェルスクリプトの入力引数をJavaアプリに渡す
       java mypkg.HelloJava "$@"
       ```
       
    2. 実行可能ファイルにする
        ```sh
        chmod +x sh/hellojava
        ```
    
    3. 実行する
    
        ```sh
        ./sh/hellojava
        ```
        結果
        ```
        Hello world
        ```



7. **メインクラス指定無しのjarファイルを作成して実行**

    1. **Jarをビルドする**

        ```sh
        jar --create --file hello_jar.jar -C bin . 
        ```
        
    2. **実行する**
       `jar`ファイルをクラスパスに追加して、`java`コマンドから実行できる
    
        ```sh
        java -cp hello_jar.jar mypkg.HelloJava
        ```
        結果
        ```
        Hello world
        ```



8. **メインクラスを指定するjarファイルを作成して実行**

    1. **Jarをビルドする**

        ```sh
        jar --create --file hello_jar.jar --main-class mypkg.HelloJava  -C bin . 
        ```

    2. **実行する**
       `-jar FILE`オプションを指定し、`java`コマンドからメインクラスを実行できる

        ```sh
        java -jar hello_jar.jar
        ```
        結果
        ```
        Hello world
        ```



