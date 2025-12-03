### 仮想モジュールとは

<img src="./img/VirtualModules/Virtual-Modules_1.svg" />

<br>

- 実在するモジュール (ファイル) ではなくコードを動的に生成されるモジュールのこと

    - 多くの場合プラグインにて生成されることが多い

---

### よくあるケース

- TypeScript で記述した仮想モジュールがエラーになる

    <img src="./img/VirtualModules/Virtual-Modules-Issue_1.svg" />

    <br>

    → JavaScritp にトランスパイルしないと動かない

<br>

- 実在するモジュールであれば、Vite のコアプラグインが TS → JS にトランスパイルしてくれるが、仮想モジュールの場合は、自分で明示的にトランスパイルをする必要がある

    <img src="./img/VirtualModules/Virtual-Modules-Issue1- Solution_1.svg" />