# eg2d

モダンなウェブブラウザ向けの軽量2Dゲームエンジンです。強力な物理演算ライブラリである[Matter.js](https://brm.io/matter-js/)をベースに構築されています。

## 特徴

-   **フルスクリーンキャンバス:** ブラウザのウィンドウ全体に広がるキャンバスを自動的に作成・管理します。
-   **物理シミュレーション:** 堅牢なMatter.jsエンジンを活用し、リアルな物理演算を実現します。
-   **入力処理:** タッチとマウスの両方の操作を組み込みでサポートしています。
-   **動的な重力:** デバイスのモーションセンサーと連携し、リアルタイムで重力を制御します。

## 要件

-   ES Modulesをサポートするモダンなウェブブラウザ。

## クイックスタート

1.  `index.html`ファイルを作成し、`eg2d.js`をモジュールとしてインポートします。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>eg2d Demo</title>
        <style>
          body { margin: 0; overflow: hidden; }
        </style>
      </head>
      <body>
        <script type="module" src="app.js"></script>
      </body>
    </html>
    ```

2.  `app.js`ファイルを作成し、ワールドをセットアップします。

    ```javascript
    import { createWorld, Matter } from './eg2d.js';

    // 1. ワールドを作成し、描画ループを定義します
    const world = createWorld(document.body, (g, engine) => {
      // この関数は毎フレーム呼び出され、オブジェクトを描画します
      g.lineWidth = 2;
      g.strokeStyle = "black";

      const bodies = Matter.Composite.allBodies(engine.world);
      for (const body of bodies) {
        g.beginPath();
        body.vertices.forEach((v, j) => {
          if (j === 0) g.moveTo(v.x, v.y);
          else g.lineTo(v.x, v.y);
        });
        g.closePath();
        g.stroke();
      }
    });

    // 2. エクスポートされたMatterオブジェクトを使用して物理ボディを作成します
    const ball = Matter.Bodies.circle(500, 100, 40, { restitution: 0.8 });
    const ground = Matter.Bodies.rectangle(500, 950, 800, 100, { isStatic: true });

    // 3. ワールドにボディを追加します
    world.add(ball);
    world.add(ground);

    // 4. (オプション) UIイベントを有効にし、クリック/タッチで新しいボールを追加します
    world.useUI();
    world.ontouch = (p) => {
      const newBall = Matter.Bodies.circle(p.x, p.y, 20 + Math.random() * 30, {
        restitution: 0.8,
      });
      world.add(newBall);
    };
    ```

## APIリファレンス

### `createWorld(parentElement, renderCallback)`

エンジンを初期化し、`parentElement`内にフルスクリーンキャンバスを作成します。

-   `parentElement` (HTMLElement): キャンバスを追加するDOM要素（例: `document.body`）。
-   `renderCallback(g, engine)` (Function): 各アニメーションフレームで呼び出される関数。
    -   `g`: 描画用のヘルパー関数が拡張された2Dキャンバスレンダリングコンテキスト。
    -   `engine`: ベースとなるMatter.jsのエンジンインスタンス。

以下のプロパティとメソッドを持つ`world`オブジェクトを返します。

### World オブジェクト

#### プロパティ

-   `.width` / `.height` (Number): 物理ワールドの論理的なサイズを取得または設定します。デフォルトは`1000x1000`で、画面に合わせてスケーリングされます。
-   `.gravity` (Vector): ワールドの重力ベクトルにアクセスします（例: `world.gravity.y = 1;`）。
-   `.engine` (Engine): 高度な制御を行うための、ベースとなるMatter.jsの`Engine`インスタンス。
-   `.render` (Object): キャンバス、オフセット、スケール情報を含む内部のレンダリングオブジェクト。

#### メソッド

-   `.add(body)`: Matter.jsのボディまたはコンポジットをワールドに追加します。
-   `.remove(body)`: Matter.jsのボディまたはコンポジットをワールドから削除します。
-   `.useRealGravity()`: デバイスのモーションセンサーに基づく重力を有効にします。一部のプラットフォーム（例: iOS）ではユーザーの許可が必要になる場合があります。
-   `.useUI()`: マウスおよびタッチ入力を有効にします。呼び出し後、ワールドオブジェクトに`ontouch`コールバックを定義してイベントを処理します。
    -   `world.ontouch = (point) => { ... }`: ワールドの論理サイズにマッピングされた座標を持つ`point`オブジェクト（`{x, y}`）を受け取るコールバック関数。

## ライセンス

MIT License — 詳細は [LICENSE](LICENSE) を参照してください。
