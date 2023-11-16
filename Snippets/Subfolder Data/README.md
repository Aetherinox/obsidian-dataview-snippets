# Obsidian: Subfolder Data
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).

<br /><br />

## About
The `Subfolder Data` snippet displays a table of contents listing based on a specified subfolder. This is useful if you want to pull a list of headers which exist on a page that is not associated to the current folder you are working in.

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

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Subfolder%20Data/images/example_1.gif"></p>

<br />

<br /><br />

---

<br /><br />

## Usage

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Install [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) *(if wanting to use notes*)
- Copy the code below and paste it in a note.

<br />

````shell
```dataviewjs
/*
    Table of Contents Script > Version 3

    This version allows for pulling subfolder notes to
    another table of contents page.

    Excludes any subfolder table of contents pages
    labeled FOLDERNAME/FOLDERNAME.md

    Change "General" to the subpage you are wanting to target
*/

const path_sub          = "General"

let count               = 0;
const path_base         = dv.current().file.path
const path_targ         = path_base.substr(0, path_base.lastIndexOf("/"));
const path_fnote_excl   = path_sub + ".md"
const filter_page       = '"' + path_targ + "/" + path_sub + '"';
const filter_folder     = path_targ + "/" + path_sub;

let p = dv.pages(filter_page)
    .where(p => p.file.name != dv.current().file.name) // Filter out the current page
    .where(p => p.file.folder == filter_folder) // Filter out folders
    .where(p => !p.file.path.endsWith(path_fnote_excl) ) // Filter folder note index
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
                const houtput = headings.slice(1) // exclude the first heading
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

Ensure you change the path to the **subfolder** you want to target:

```javascript
const path_sub          = "General"
```

<br />

In the screenshot example, I changed it `Plugins` because the Plugins folder is a subfolder of what I wanted to target and build a table of contents from. You can use an entirely different path to whatever folder you want to generate a list of headers from. 

```javascript
const path_sub          = "Plugins"
```

<br />

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Subfolder%20Data/images/install_1.gif"></p>

<br />

Create a new file named whatever (`toc.css` in our example).

Copy the code below and paste it into the new `toc.css` file which should be in `YourVaultName/.obsidian/snippets/toc.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Subfolder%20Data/images/install_2.png"></p>

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

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Subfolder%20Data/images/install_3.gif"></p>

<br />

You should see a table of contents which is populated with the headings of your subfolders.