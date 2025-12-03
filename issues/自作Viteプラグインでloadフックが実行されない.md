### 事象

- 自作プラグインを作成 & load フックを実装したが、トリガーされない

    ```ts
    //myPlugin.ts
    import type { Plugin } from "vite";

    export const myPlugin = (): Plugin => {
        return {
            name: "myPlugin",
            load: () => {
                //単にログを出すだけ
                console.log("load start")
            }
        };
    }
    ```

    <br>

    ```js
    //vite.config.js
    import { defaultConfig } from "vite";
    import { myPlugin } from "./plugins/myPlugin";

    export default defaultConfig({
        plugins: [
            myPlugin()
        ]
    });
    ```

    <br>

    <img src="./img/Issue-Load-Hook_1.svg" />

---

### 原因

- 開発ビルド (`vite` コマンドでのビルド) の場合、モジュール (ファイル) にリクエストが来ることでトリガーされる

<br>
<br>

参考サイト

[Vite - 共通のフック](https://ja.vite.dev/guide/api-plugin#共通のフック)

---

### 解決策

- 今回はバグではなく仕様なので、解決も何もない