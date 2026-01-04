### 事象

- Vite + React + Typescript プロジェクトを作成し、以下のようなグローバル変数 (Context) コンポーネントファイルを作成した

    - グローバル変数を扱いたくて createContext を使って Context を作成し、 Provider コンポーネントも作成

        ```ts
        //TodoTitleProvider.tsx
        import { useState, createContext, type ReactNode } from "react";
        
        //★★Fast refresh only works when a file only exports components. Move your React context(s) to a separate file.★★エラー
        export const todoTitleContext = createContext({});
        
        const TodoTitleProvider = ({children}: {children: ReactNode}) => {
            const [todoTitle, setTodoTitle] = useState("");
            
            return <todoTitleContext.Provider value={{todoTitle, setTodoTitle}}> {children} </todoTitleContext.Provider>
        };
        
        export default TodoTitleProvider;
        ```

    <br>

    - ちなみに、以下のように普通の変数の export とコンポーネントの export は共存できた

        ```ts
        //Test.tsx

        //↓は問題なくexport可能
        export normalVariable = "hello";

        const TestComponent = () => {
            return <></>;
        };
        export default TestComponent
        ```

---

### 原因


- Vite が使う jsx/tsx コンパイルのためのプラグイン @vitejs/plugin-react が発している警告

<br>

- Context を定義しているファイルで「コンポーネント以外（createContext）」を export してはいけない

    <img src="./img/Issue-@vitejs_plugin-react_1.svg" />

    引用: [@vitejs/plugin-react - Consistent components exports](https://www.npmjs.com/package/%40vitejs/plugin-react?utm_source=chatgpt.com#user-content-consistent-components-exports)

---

### 解決策

- createContext で作成した Context を export するファイルと、 Provider コンポーネントファイルとで切り分ける

    ```ts
    //TodoTitleContext.ts (Contextの作成&export)
    //★createContext({}) は型安全ではないので 型付きで定義するのが重要
    import { createContext } from "react";

    type StateSetter<T> = React.Dispatch<React.SetStateAction<T>>;

    export type TodoTitleContextType = {
        todoTitle: string;
        setTodoTitle: StateSetter<string>;
    };

    export const TodoTitleContext = createContext<TodoTitleContextType | undefined>(undefined);
    ```

    <br>

    ```ts
    //TodoTitleProvider.tsx (Providerコンポーネントの定義&export)
    import { useState, type ReactNode } from "react";
    import { TodoTitleContext } from "./TodoTitleContext";

    const TodoTitleProvider = ({ children }: { children: ReactNode }) => {

        const [todoTitle, setTodoTitle] = useState("");

        return (
            <TodoTitleContext.Provider value={{ todoTitle, setTodoTitle }}>
            {children}
            </TodoTitleContext.Provider>
        );
    };

    export default TodoTitleProvider;
    ```

    <br>

    ```tsx
    //他のフツーのコンポーネント (Contextを参照する側)
    import { useContext } from "react";
    import { TodoTitleContext } from "./TodoTitleContext"; //作成したContextモジュールのimport

    export const useTodoTitle = () => {
        const {todoTitle, setTodoTitle} = useContext(TodoTitleContext); //ここでグローバル変数の利用

        return <></>;
    };
    ```