# 大阪大学 ICS 卒業論文・修士論文 LaTeX テンプレート

大阪大学 基礎工学部 情報科学科 (ICS) 向けの、卒業論文・修士論文用 LaTeX テンプレートです。

---

## 目次

- [はじめに：環境を選ぶ](#intro)
- [セットアップ：リポジトリの作成](#setup)
- [構成ファイルについて](#structure)
- [コースA：VS Code + WSL（推奨・ローカル）](#course-a)
- [コースB：Overleaf（ブラウザ）](#course-b)
- [よくあるトラブル](#troubles)

---

<a id="intro"></a>

## はじめに：環境を選ぶ

執筆環境は以下のどちらかを選んでください。

| 環境 | 特徴 | 向いている人 |
| :--- | :--- | :--- |
| **VS Code + WSL** | ローカルで動作。高速で、Git管理もしやすい。 | 今後のために環境構築に慣れておきたい人 |
| **Overleaf** | ブラウザだけで完結。環境構築不要。 | とにかくすぐに書き始めたい人 |

---

<a id="setup"></a>

## セットアップ：リポジトリの作成

まず、自分用のリポジトリを作成します。

1. テンプレートリポジトリを開く：  
   - <https://github.com/izulabo-ou/thesis-template>
2. 画面右上の **`Use this template`** をクリックし、`Create a new repository` を選択。
3. **`Repository name`** を以下の命名規則に従って設定する。
   - **卒論の場合**：`thesis-bachelor-<year>-<name>`（例：`thesis-bachelor-2023-hiraoka`）
   - **修論の場合**：`thesis-master-<year>-<name>`（例：`thesis-master-2025-hiraoka`）

> [!IMPORTANT]
> **研究室の資産として残すために**  
> Overleafで作業する場合も、論文完成後（または定期的に）GitHubへアップロード・同期するようにしてください。

---

<a id="structure"></a>

## 構成ファイルについて

- **`main.tex`**：論文本文のメインファイル
- **`biblio.bib`**：参考文献データ（BibTeX形式）
- **`ics-thesis.sty`**：ICS論文用スタイルファイル
- **`figure/`**：画像ファイル置き場
- **`out/`**：ビルド生成物（PDF / log / aux など）の出力先

---

<a id="course-a"></a>

## コースA：VS Code + WSL（推奨・ローカル）

Windows上のVS CodeからWSL（Ubuntu等）に接続し、Linux側でビルドします。

> [!NOTE]
> Macユーザーへ：基本的には MacTeX を導入すれば動作しますが、本ガイドのサポート対象外です（個別対応はしません）。(AIに聞いてくれ！)

### 1. VS Code と拡張機能（Windows側）

以下の拡張機能をインストールしてください。

- **Remote - WSL**（Microsoft）
- **LaTeX Workshop**（James-Yu）

WSLがまだ入っていない場合は、PowerShell（管理者権限）で以下を実行して再起動してください。

```powershell
wsl --install
```

### 2. 環境構築（WSL / Ubuntu側）

WSLのターミナルを開き、必要なパッケージをインストールします。

```bash
sudo apt update
sudo apt install -y \
  latexmk perl \
  texlive-lang-japanese \
  texlive-latex-base texlive-latex-recommended texlive-latex-extra \
  texlive-science texlive-pictures \
  texlive-fonts-recommended texlive-fonts-extra \
  texlive-bibtex-extra biber \
  ghostscript
```

### 3. プロジェクトを開く

VS Code 左下の緑色のアイコン（リモートインジケーター）をクリックし、WSLに接続します。

WSL側のディレクトリ（例：`~/workspace/thesis` など）にリポジトリを `git clone` して開きます。

> [!WARNING]
> `/mnt/c/`（Windows側の領域）で作業するとファイル監視が効かず、ビルドが遅くなりがちです。  
> **必ず WSL側のホームディレクトリ配下**（例：`/home/<user>/...`）で作業してください。

### 4. VS Code 設定（settings.json）

`.vscode/settings.json` またはユーザー設定（`Ctrl+,` → 右上の「設定(JSON)を開く」）に、以下を追記します。  
これにより `Ctrl+Alt+B` でビルドできるようになります。

```json
{
  "latex-workshop.latex.tools": [
    {
      "name": "latexmk (pdfdvi, out)",
      "command": "latexmk",
      "args": [
        "-pdfdvi",
        "-outdir=out",
        "-auxdir=out",
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "%DOC%"
      ]
    },
    {
      "name": "latexmk (clean, out)",
      "command": "latexmk",
      "args": [
        "-c",
        "-outdir=out",
        "-auxdir=out",
        "%DOC%"
      ]
    }
  ],
  "latex-workshop.latex.recipes": [
    {
      "name": "latexmk (pdfdvi + clean, out)",
      "tools": ["latexmk (pdfdvi, out)", "latexmk (clean, out)"]
    },
    {
      "name": "latexmk (pdfdvi, out)",
      "tools": ["latexmk (pdfdvi, out)"]
    }
  ],
  "latex-workshop.latex.recipe.default": "latexmk (pdfdvi + clean, out)",
  "latex-workshop.latex.outDir": "%DIR%/out",
  "latex-workshop.view.pdf.internal.synctex.keybinding": "double-click"
}
```

> [!TIP]
> SyncTeX（PDF⇔ソースのジャンプ）を使いたい場合：  
> デフォルト設定（`pdfdvi + clean`）ではビルド後に一時ファイルが削除され、ジャンプ機能が動かないことがあります。  
> 執筆中は `latex-workshop.latex.recipe.default` を **`latexmk (pdfdvi, out)`** に変更するのがおすすめです。

### 5. 執筆開始

1. `main.tex` を開き、タイトル・著者名・指導教員名・提出日を埋める
2. **卒論**：`\pagestyle{bachelorthesis}` を有効化  
   **修論**：`\pagestyle{masterthesis}` を有効化
3. `Ctrl+Alt+B` でビルドし、`out/main.pdf` が生成されることを確認

---

<a id="course-b"></a>

## コースB：Overleaf（ブラウザ）

### 1. プロジェクト作成

1. Overleaf で `New Project` → `Upload Project` を選択
2. GitHubからcloneしたファイル一式、またはダウンロードしたZipをアップロード

### 2. 設定（重要）

左上の `Menu` から、以下の設定を変更してください。ここを間違えると日本語が表示されません。

- **Compiler**：`LaTeX`（※ pdfLaTeX ではありません）
- **TeX Live version**：最新（推奨）
- **Main document**：`main.tex`

### 3. 執筆開始

1. `main.tex` を開き、タイトル・著者名・指導教員名・提出日を埋める
2. **卒論**：`\pagestyle{bachelorthesis}` を有効化  
   **修論**：`\pagestyle{masterthesis}` を有効化
3. 緑色の `Recompile` を押してPDF生成

---

<a id="troubles"></a>

## よくあるトラブル

- **ビルドエラーが消えない**
  - `out/` フォルダの中身（またはOverleafのキャッシュ）を削除してから再ビルドしてください。
- **引用が `[?]` のまま**
  - `biblio.bib` に該当するIDが存在するか確認してください。
  - LaTeXは参照解決のために複数回ビルドが必要な場合があります（`latexmk` を使っていれば自動で行われます）。
- **所属やフォーマットを変えたい**
  - `ics-thesis.sty` 内の `\def\institutestr{...}` などを編集してください。
- **文字化けする / PDFが真っ白**
  - ファイルの文字コードが UTF-8 になっているか確認してください。
  - Overleafの場合、Compilerが `LaTeX` になっているか再確認してください。
