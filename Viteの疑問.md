### 疑問1

- Vite プロジェクト (TypeScript) を作成すると、package.json の scripts フィールドに以下の記述が追加される

    ```json
    {
        "scripts": [
            "dev": "vite",
            "build": "tsc && vite build" //なんでtscがついてるの?
        ]
    }
    ```

    →なんで 本番ビルドコマンド (`vite build`) に `tsc &&` が付いているの?

<br>

- まず、`tsc && vite build` コマンドの意味は

    1. 最初に tsc コンパイラで ts ファイルの型チェック + js ファイルにコンパイル

    2. tsc コマンド成功がしたら、`vite build` で本番ビルドを実行する

<br>

- ★Vite での本番ビルド (`vite build`) は内部で Rollup を使っている

    - Rollup でのビルドは ts ファイルの型チェックを行わないので、ビルド時に型エラーを検出できない

    - **よって、他のツールで型エラーを検出する必要がある**

<br>

#### 注意点

- ★Vite はビルド時に tsc でトランスパイルされた js ファイルを使わない

    - 従って、 `tsc && vite build` では tsc コマンドで無駄に js ファイルが出力されてしまう
 
    - しかし、Vite のセットアップで作成された tsconfig.app.json 中に `"noEmit": true` が設定されているので、 tsc コマンドを実行しても js ファイルが出力されないようになっている

        → 公式では package.json の scripts フィールドに定義する `build` コマンドは以下のように定義するべきと紹介している

        ```json
        {
            "scripts": [
                "build": "tsc --noEmit && vite build"
            ]
        }
        ```

<br>
<br>

参考サイト

[開発ツールVite 第1回 開発時ビルドの高速化と本環境向けの施策](https://www.codegrid.net/articles/2022-vite-1/?utm_source=chatgpt.com)