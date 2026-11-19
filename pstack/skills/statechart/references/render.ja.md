# チャートの描画

表示する各 `.mmd` ファイルを npm パッケージ `pele@1.9.0` で描画し、SVG をユーザーに渡す。Pele はリポジトリの外で一度だけインストールする。各 SVG を同名で `.mmd` ファイルの隣に書き出す。API は https://pele.run/api にある。`render` のオプションとして `{ limit: 1_000_000, outputLimit: 8_000_000, maxEdges: 10_000 }` を渡す。Pele のデフォルトは 500 状態のチャートに対しては小さすぎる。Pele が `PeleError` をスローした場合、Mermaid テキストとエラーをユーザーに表示する。

## 色

Pele は Mermaid の `theme` や `themeVariables` を無視する。代わりに `.pele` に対して `--pele-*` CSS 変数を設定する。https://pele.run/theming にそれらが列挙されている。コマンドは各状態にそのグループの色クラスを付与する。例えば `class sN teal` である。不正な状態には `class sN breaks` または `class sN mayBreak` を、バグへの最短経路上の各状態には `class sN bugPath` を、PROPERTY の反例が永遠に繰り返す各状態には `class sN repeats` を付ける。Pele はこれらのクラスを状態のノードに適用する。CSS でクラスに色を付ける。

`.mmd` ファイルには `breaks` と `mayBreak` の `classDef` 行が含まれているので、任意の Mermaid ビューワでバグマークが表示される。グループカラー、`bugPath`、`repeats` の `classDef` は含まれない。Pele は各 `classDef` の色をノードの `style` 属性として書き出し、`style` 属性は通常の CSS ルールより優先される。そのため、以下のマーク用ルールは `!important` を使う。これがないと、ダークテーマでもライトのマーク色が残ってしまう。バグマークは塗りつぶしと文字色を設定し、`bugPath` と `repeats` は境界線を設定する。マークのルールはグループカラーの後に置き、グループカラーの上にマークが表示されるようにする。

これらの色は提案である。これは旧スキルの色である。各 SVG の先頭に `<style>` 要素として配置する。すると各 SVG はビューワのライト・ダーク設定に従う。

```css
.pele {
  --pele-bg: #ffffff;
  --pele-fg: #1c1c1c;
  --pele-muted: #656565;
  --pele-line: #808080;
  --pele-surface: #fcfcfc;
  --pele-surface-alt: #f1f1f1;
  --pele-border: #c9c9c9;
}
.pele .blue rect { fill: #c3e0ff; }
.pele .orange rect { fill: #f0d3bf; }
.pele .purple rect { fill: #e3d5ff; }
.pele .yellow rect { fill: #f0dcbf; }
.pele .teal rect { fill: #bfe3df; }
.pele .tan rect { fill: #e5d9ce; }
.pele .pink rect { fill: #ffcbe6; }
.pele .breaks rect {
  fill: #ffecee !important;
  stroke: #e72e3b !important;
  stroke-width: 2.5px !important;
}
.pele .breaks text {
  fill: #cd2834 !important;
}
.pele .mayBreak rect {
  fill: #fff3e6 !important;
  stroke: #e8730c !important;
  stroke-width: 2.5px !important;
}
.pele .mayBreak text {
  fill: #b85a06 !important;
}
.pele .bugPath rect {
  stroke: #1680f3 !important;
  stroke-width: 2.5px !important;
}
.pele .repeats rect {
  stroke: #9b35d6 !important;
  stroke-width: 2.5px !important;
  stroke-dasharray: 6 3;
}
@media (prefers-color-scheme: dark) {
  .pele {
    --pele-bg: #1f1f1f;
    --pele-fg: #f5f5f5;
    --pele-muted: #acacac;
    --pele-line: #939393;
    --pele-surface: #121212;
    --pele-surface-alt: #2c2c2c;
    --pele-border: #4e4e4e;
  }
  .pele .blue rect { fill: #1b3857; }
  .pele .orange rect { fill: #573117; }
  .pele .purple rect { fill: #3c2e57; }
  .pele .yellow rect { fill: #573d17; }
  .pele .teal rect { fill: #174641; }
  .pele .tan rect { fill: #3d3127; }
  .pele .pink rect { fill: #57233e; }
  .pele .breaks rect {
    fill: #2e2021 !important;
    stroke: #cf2b35 !important;
  }
  .pele .breaks text {
    fill: #ff6463 !important;
  }
  .pele .mayBreak rect {
    fill: #2e2619 !important;
    stroke: #d0700f !important;
  }
  .pele .mayBreak text {
    fill: #ffa257 !important;
  }
  .pele .bugPath rect {
    stroke: #116dd2 !important;
  }
  .pele .repeats rect {
    stroke: #9a3fcf !important;
  }
}
```
