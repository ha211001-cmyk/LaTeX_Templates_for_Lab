# 卒論・修論要旨テンプレート

要旨（アブストラクト）は大学指定の書式に合わせて調整済みのテンプレートです。

## ファイル構成

| ファイル | 説明 |
| --- | --- |
| `abstract_main.tex` | メインファイル（ここに原稿を書く） |
| `header.tex` | プリアンブル（余白・字間・行送り調整含む） |
| `latexmkrc` | latexmk のビルド設定（uplatex + dvipdfmx） |
| `bibfile.bib` | 参考文献データベース |
| `umlab.bst` | 参考文献スタイル |
| `fig/` | 図の画像（`wind_power.JPG` / `wind_power.png` / `AngularVelocity.pdf`） |

## 使い方

1. `abstract_main.tex` 冒頭の `\title`・`\author`・`\abstracttxt`（アブストラクト本文）・`\supervisor` を書き換える
2. 本文のサンプルを自分の内容に置き換える
3. 図は `fig/` に置き、`\includegraphics[width=...]{fig/ファイル名}` で参照する

## コンパイル

このフォルダで次を実行します。

```sh
latexmk abstract_main.tex
```

生成物は `abstract_main.pdf` です。
VS Code + LaTeX Workshop なら保存時に自動コンパイルされます。

## 書式

- A4 **二段組 9pt**・左右余白 15mm・段間 10mm
- **1 段あたり全角 26 文字・1 ページ 59 行**に収まるよう字間・行送りを自動調整（`header.tex` の `\GraduateSetKanjiskipForTwentySix`）
- ページ番号はデフォルトで非表示（`\pagestyle{empty}`）
- 参考文献は `\bibliography{bibfile}` 方式（`umlab.bst`）
- 図表のキャプションは英語表記（Fig. / Table）が基本

> 書式は大学・年度によって変わることがあるため、**最新の指定と必ず照合**してください。
