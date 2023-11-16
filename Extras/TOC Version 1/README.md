# Obsidian: Table of Contents: Version 1
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).
<br />
This snippet requires the [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes).

<br /><br />

## About
The `Table of Contents: Version 1` snippet displays a table of contents. It compiles a list of all your folder's current subpages and pulls the headers from each page to display in a simple and neat list.

<br />

The code for this version should be pasted inside a **Folder Note**. It will fetch all of the files that exist inside that folder.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |
| [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) | ![GitHub release](https://img.shields.io/github/v/release/LostPaul/obsidian-folder-notes?label=v&color=ba0f56) |

<br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/TOC%20Version%201/images/example_1.gif"></p>

<br />

<br /><br />

---

<br /><br />

## Usage

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Install [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes)
- Right-click on a folder, select `Folder Note Commands` and select `Create Folder Note`
- Copy the code below and paste in Obsidian folder note:

<br />

````javascript
```dataviewjs

/*
    Table of Contents Script > Version 1

    This version requires the plugins:
        https://github.com/LostPaul/obsidian-folder-notes
        https://github.com/blacksmithgu/obsidian-dataview

    Create a new folder, right-click, and create folder note.
    Click inside the folder note and paste the code below.

    Inside that folder, place the files you want the table of
    contents to display. Make sure each file contains headers.
*/

let count               = 0;
const path_base         = dv.current().file.path
const path_targ         = path_base.substr(0, path_base.lastIndexOf("/"));
const path_sub          = ""

const filter_page       = '"' + path_targ + "" + path_sub + '"';
const filter_folder     = path_targ + path_sub;

let p = dv.pages(filter_page)
    .where(p => p.file.name != dv.current().file.name) // Filter out the current page
    .where(p => p.file.folder == filter_folder) // Filter out folders
    .sort(p => p.file.path)
    .forEach(p =>
    {
        dv.header(4, p.file.name); // display page name as header
        const cache = this.app.metadataCache.getCache(p.file.path);

        if (cache)
        {
            const headings = cache.headings; // get headings from cache

            if (headings)
            {
                const houtput = headings.slice(0) // exclude the first heading
                .filter(h => h.level <= 6)
                .map(h =>
                {
                    var file_head     = h.heading
                    var header_skip   = file_head.replace(/ /g,"_").toLowerCase();
                    if (header_skip === "table_of_contents" || header_skip === "toc")
                    {
                        return ""
                    }

                    count++;

                    // Determine indentation based on heading level
                    let indent        = " ".repeat(h.level);
                    var file_name     = p.file.name;
                    
                    // remove backticks and tag symbols
                    var file_head     = file_head.replace(/`/g, '');
                    var file_head     = file_head.replace(/#/g, '');
                    var file_title    = h.heading.split('#')[0];

                    let objLink       = "[[" + file_name + "#" + file_head + "|" + file_title + "]]";

                    if ( h.level == 1 )
                        return indent + "- " + objLink + "";
                    else if ( h.level == 2 )
                        return indent + " - <span class='toc_h2'>" + objLink + "</span>";
                    else if ( h.level == 3 )
                        return indent + "  - <span class='toc_h3'>" + objLink + "</span>";
                    else if ( h.level == 4 )
                        return indent + "   - <span class='toc_h4'>" + objLink + "</span>";
                    else if ( h.level == 5 )
                        return indent + "    - <span class='toc_h5'>" + objLink + "</span>";
                    else if ( h.level == 6 )
                        return indent + "     - <span class='toc_h6'>" + objLink + "</span>";
                    else
                        return indent + "- " + objLink;
                })
                .join("\n")

                dv.el("div", houtput);
                dv.el("div", "<br />");
            }
        }
    });

    /*
        display NO RESULTS
    */

    if (count === 0)
    {
        const rootNode = dv.el("div", "No results", { cls: "toc_results_none" });
        rootNode.setAttribute("style", "text-align:center;");
    }
```
````

<br />

If you want to target a subfolder / path, you can modify the variable `path_sub`. If you leave it blank, it will target the folder you paste the code in.

```javascript
const path_sub          = "Path/To/Subfolder"
```

<br />

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/TOC%20Version%201/images/install_1.gif"></p>

<br />

Create a new file named whatever (`toc.css` in our example).

Copy the code below and paste it into the new `toc.css` file which should be in `YourVaultName/.obsidian/snippets/toc.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/TOC%20Version%201/images/install_2.png"></p>

<br />

```css
/*
    Snippet: Table of Contents
*/

    /*
        toc > header 2
    */

        .toc_h2 a
        {
            color: #7fa8f5 !important;
            font-size: 10pt;
            font-weight: lighter;
            line-height: 20px;
        }

    /*
        toc > header 3
    */

        .toc_h3 a
        {
            color: #f85289 !important;
            font-size: 9pt;
            font-weight: lighter;
            line-height: 20px;
        }

    /*
        toc > header 4, 5, 6
    */

        .toc_h4 a, .toc_h5 a, .toc_h6 a
        {
            color: #969696 !important;
            font-size: 8pt;
            font-weight: normal;
            line-height: 20px;
        }

    /*
        toc > bad links > path
    */

        .toc_badpaths_path, .toc_badpaths_path a
        {
            color: #adadad !important;
            font-size: 8pt;
            font-weight: normal;
            line-height: 20px;
        }

    /*
        toc > no results
    */

        .toc_results_none
        {
            text-align: center;
            font-size: 12pt;
        }
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `toc.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/TOC%20Version%201/images/install_3.gif"></p>

<br />

You should see a table of contents which is populated with the headings of your subfolders.

<br /><br />

---

<br /><br />

## Customization
### How to make page title above the each list appear bigger
In your code, locate
```javascript
dv.header(4, p.file.name);
```

The `4` stands for `H4` or header 4. To make the text bigger, change that number to `3 or lower`. Overall, it accepts any number between `1 - 6`, with 1 being the biggest and 6 being the smallest text size.

<br />
<br />

### Make each page title not display as a listed item
Locate the code toward the top:
```javascript
dv.header(4, p.file.name);
```
and replace it with:
```javascript
dv.el("div", p.file.name);
```

This will force each page title to display in a `div` and not as header object.

<br />
<br />

### My list is cutting off the first header of each page
Locate the following code:
```javascript
const houtput = headings.slice(1)
```

The number `1` represents how many headers on the page that should be excluded from the first going from the top down. If you enter the number `0`, then no headers will be filtered out / all will show in the list. If you enter `3`, then the first three headers of each page will not appear in the table of contents list.

To disable any headers from being excluded in the list, change the code above to the following:
```javascript
const houtput = headings.slice(0)
```

<br />
<br />

### Exclude certain headers from appearing in the list
Much like the example above which allows you to filter from from the top, you can also filter exactly which headers display in the list with the following code:
```javascript
.filter(h => h.level <= 6)
```

The number `6` represents which headers will be included in the list. If you change the value to a `4`, that means any sections of your page with headers 5 or 6 will not show.
```
##### This is H5
###### This is H6
```

<br />
<br />

### Making the gap between each list smaller/bigger
You need to modify the following:
```javascript
dv.el("div", "<br /><br />");
```
Each `<br />` is a line break. The more breaks you add, the larger the gap between each list. Remove each break to make the gap smaller.

<br />
<br />