# 研究報告書テンプレート

ゼミや進捗報告など、手軽に使う報告書向けテンプレートです。

## ファイル構成

| ファイル | 説明 |
| --- | --- |
| `report_main.tex` | メインファイル（ここに原稿を書く） |
| `report_preamble.tex` | プリアンブル（余白・パッケージ・自作コマンド・定理環境） |
| `latexmkrc` | latexmk のビルド設定（uplatex + dvipdfmx） |
| `bibfile.bib` | 参考文献データベース |
| `umlab.bst` | 参考文献スタイル |
| `fig/` | 図の画像（`wind_power.JPG` / `wind_power.png` / `AngularVelocity.pdf`） |

## 使い方

1. `report_main.tex` 冒頭の `\title`・`\author`・`\date` を書き換える
2. 本文のサンプル（LaTeX の概要・表と図・数式・参考文献・報告書の章立て）を自分の内容に置き換える
3. 図は `fig/` に置き、`\includegraphics[width=...]{fig/ファイル名}` で参照する

## コンパイル

このフォルダで次を実行します。

```sh
latexmk report_main.tex
```

生成物は `report_main.pdf` です。
VS Code + LaTeX Workshop なら保存時に自動コンパイルされます。

## 書式

- A4 **二段組 12pt**、余白は上 1.5cm / 下 1.0cm / 左右 1.4cm
- 参考文献は `\bibliography{bibfile}` 方式（`umlab.bst`）
