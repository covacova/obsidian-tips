
# Extract Tagged Lines in Notes

> Obsidian DataviewJS snippet  
> Created: 2025-05-08  
> Tags: `#dataviewjs`, `#tag-query`, `#obsidian`

## 📝 概要 / Description

Obsidian Valut 内で、特定のタグ (例：`#idea`) が含まれるノートとそのタグ付き文を抜き出して表形式で表示するクエリです。

This DataviewJS snippet collects notes that include a specific tag (e.g., `#idea`)  
and extracts only the **tagged lines** from those notes. Output will be a table-style summary of the note and the tagged line(s).

---

## 💻 使用例 / Usecase

- タグのついた行を抽出して振り返り・集計・可視化する用途に便利
	- `#idea` タグを振り返り、そのノートにアクセスして次のステータスである `#ongoing` に変更する、という運用が楽になる
- タグは snippet 内の冒頭で変更できるような汎用性を持たせています。

---

## 🔍 クエリ本体 / DataviewJS Snippet

~~~
```dataviewjs
// ここでタグを指定 here define the tag you want to search and output
let tagName = "#idea"; // ここを変更するだけで対象タグを変えられる

let rows = [];
for (let page of dv.pages(tagName)) {
    let content = await dv.io.load(page.file.path);
    // codeblock, inline code 内のタグ文字列を対象から外す Exclude the specified tag in code blocks and inline code
    let tags = content.replace(/```[\s\S]*?```/g, '') // Remove code blocks
                       .match(new RegExp(`(?:^|\\n)(?!.*\`.*${tagName}.*\`)[^\\n]*${tagName}[^\\n]*`, 'g'));
    // HTMLエスケープを解除し、文の間に改行を追加
    let formattedTags = tags ? tags.map(tag => tag.replace(/^\s*-\s*/gm, '').replace(/\\n/g, '<br>')).join("<br><br>") + "<br><br>" : "No tags found";
    rows.push([`[[${page.file.name}]]`, formattedTags]);
}

// タイトルを表の前の冒頭に示すだけの構成にする Output the title before the table
dv.paragraph(`**\`${tagName}\` タグ付けされたノートと文**`);

// 表にスタイルを追加して、ノート間に薄い線を挿入
dv.table(["Title", "Tagged Texts"], rows, { tableClass: "custom-table" });

// CSSを追加して、特定のテーブルにだけ薄い線を挿入
const style = document.createElement('style');
style.textContent = `
.custom-table {
    border-collapse: collapse;
    width: 100%;
}
.custom-table tr {
    border-bottom: 1px solid #ddd;
}
.custom-table tr:last-child {
    border-bottom: none;
}
`;
document.head.appendChild(style);
```
~~~

---

## 🗒 注意点 / Notes

- `dv.io.load()` を使ってノートの中身を読み込むため、**非同期処理を含むDataviewJSが有効である必要があります**
- コードブロックやインラインコード内のタグは自動で除外される設計です（意図せぬマッチを防ぎます）
- `let tagName = "#idea"` の部分を変更すれば、他のタグにもすぐ対応可能です(例：`#gear`, `#todo`)
- HTMLエスケープを解除したうえで `<br>` を差し込んでおり、複数の文も整った表示になります
- 表の見た目を整えるCSSもスニペット内で自動追加されますが、**Obsidianのテーマや環境によってはCSSが効かない場合があります**
- 行頭に `- ` がついているリスト項目は、表示時に除去されて見やすく整形されます
---

## 📂 使い方 / How to Use

1. Dataviewプラグインが有効になっていることを確認
2. このコードブロックを任意のノートに貼り付ける
3. `#idea`  あるいはご自身で設定した `let tagName = "#hogehoge"` のタグが含まれるノートと、該当行が表示されます

---

## ✅ 動作環境 / Compatibility

このスニペットは、以下のバージョンで動作確認を行いました：

- **Obsidian v1.8.10**
- **Dataview Plugin v0.5.68**

> 💡 ご利用の環境によっては、テーマや他のプラグインの影響で表示に差が出る場合があります。

### ✅ 最低動作要件（推定）

- **Obsidian v1.1.0 以上**  
  → HTML要素のスタイル反映やDataviewJSの安定動作のため

- **Dataview Plugin v0.5.0 以上（必須）**  
  → `dv.io.load()` および `await` による非同期処理対応が導入されたため

### ⚠️ バージョンが古いと起こるかもしれないこと

- `dv.io.load()` を使用する部分でエラーが出る
- 非同期処理 (`await`) が正しく動作しない
- テーブルのカスタムCSSが効かない、またはレイアウトが崩れる

> ✅ **困ったときは**：
> - Dataviewプラグインを最新版にアップデートしてみてください
> - Obsidianのバージョンが古い場合、CSSスタイルの反映がうまくいかないことがあります

---

## 📌 改良アイデア / Future Ideas

- 特定のフォルダ内に限定して抽出する

---

## 🛡 ライセンス

MIT License

---
