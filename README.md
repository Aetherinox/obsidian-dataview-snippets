# Obsidian: Table of Contents Snippet
These snippets require you to download a copy of [Obsidian.md](obsidian.md/)

<br /><br />

## About
This is a very simple **Table of Contents / TOC** script that I use in my Obsidian docs which auto-generates a list of headers and links associated to the sub-pages that you have created in your vault.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | v1.3.7 |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | v0.5.56 |
| [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) | v1.5.12 |

<br />

Select which version you want to install:

| Version | Desc |
| --- | --- |
| [Version 1](#version-1) | Requires [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) + [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) |
| [Version 2](#version-2) | Requires [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) only |

<br /><br />

## Version 1
If you do not want to install the [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes), use [Version 2](#version-2) instead.
This requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) and [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) to run.

<br />

### Usage
- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Install [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes)
- Right-click on a folder, select `Folder Note Commands` and select `Create folder note`
- Copy the code below and paste in Obsidian folder note.

<br />

````javascript
```dataviewjs

const page_sub        = "" // Edit if wanting to bypass to a certain subpage

// ------------------------------------
// No need to edit below this section
// ------------------------------------

const path_base       = dv.current().file.path
const path            = path_base.substr(0, path_base.lastIndexOf("/"));

const filter_page     = '"' + path + "" + page_sub + '"';
const filter_folder   = path + page_sub;

let p = dv.pages(filter_page)
    .where(p => p.file.name != dv.current().file.name) // Filter out the current page
    .where(p => p.file.folder == filter_folder) // Filter out folders
    .sort(p => p.file.path)
    .forEach(p =>
    {
        dv.header(4, p.file.name); // display page name as header

        const cache = this.app.metadataCache.getCache(p.file.path); //get metadata cache for the page

        if (cache)
        {
            const headings = cache.headings; // get headings from cache

            if (headings)
            {
                const houtput = headings.slice(1) // exclude the first heading
                    .filter(h => h.level <= 6) // filter headings based on level
                    .map(h =>
                    {
                        // Determine indentation based on heading level
                        let indent        = " ".repeat(h.level);
                        var file_name     = p.file.name;
                        
                        // remove backticks and tag symbols
                        var file_head     = h.heading
                        var file_head     = file_head.replace(/`/g, '');
                        var file_head     = file_head.replace(/#/g, '');
                        var file_title    = h.heading.split('#')[0];

                        let objLink       = "[[" + file_name + "#" + file_head + "|" + file_title + "]]";

                        if ( h.level == 1 )
                            return indent + "- <sub>" + objLink + "</sub>";
                        else if ( h.level == 2 )
                            return indent + " - <sub>" + objLink + "</sub>";
                        else if ( h.level == 3 )
                            return indent + "  - <sub>" + objLink + "</sub>";
                        else if ( h.level == 4 )
                            return indent + "   - <sub>" + objLink + "</sub>";
                        else if ( h.level == 5 )
                            return indent + "    - <sub>" + objLink + "</sub>";
                        else if ( h.level == 6 )
                            return indent + "     - <sub>" + objLink + "</sub>";
                        else
                            return indent + "- " + objLink;
                    })
                    .join("\n") // Join the formatted headings with newlines
                    
                dv.el("div", houtput); // Display the formatted headings as a div
                dv.el("div", "<br /><br />"); // insert line-break between sections
            }
        }
    });
```
````

<br />

### Example

![R5Ycywr](https://github.com/Aetherinox/obsidian-tableofcontents/assets/118329232/591730dd-605d-4c26-86f2-f4ee675d589e)

<br />

If you don't want to target every file assigned to that folder, you can target a sub-folder by editing the code above and entering the name of the sub-folder starting with a forward-slash `/`

<br />

![KiWPYV0](https://github.com/Aetherinox/obsidian-tableofcontents/assets/118329232/f38ce5c7-14d5-4f2e-8b8c-f25e2539ac3a)

<br /><br />

---

<br /><br />

## Version 2
This version can be implemented directly into the very top of each note in order to outline all the separate headers that exist on that same page.
This requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) to run.

<br />

### Usage
- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Create a new note
- Copy the code below and paste it at the top of the note.
- Continue to create your different headers lower down on the same page.

<br />

````javascript
```dataviewjs

const page_sub        = "" // Edit if wanting to bypass to a certain subpage

// ------------------------------------
// No need to edit below this section
// ------------------------------------

const path_base       = dv.current().file.path
const path            = path_base.substr(0, path_base.lastIndexOf(".md"));

const filter_page     = '"' + path + "" + page_sub + '"';
const filter_folder   = path + page_sub;

let p = dv.pages(filter_page)
    // .where(p => p.file.name != dv.current().file.name) // Filter out the current page
    //.where(p => p.file.folder == filter_folder) // Filter out folders
    .sort(p => p.file.path)
    .forEach(p =>
    {
        //dv.header(4, p.file.name); // display page name as header

        const cache = this.app.metadataCache.getCache(p.file.path); //get metadata cache for the page

        if (cache)
        {
            const headings = cache.headings; // get headings from cache

            if (headings)
            {
                const houtput = headings.slice(0) // exclude the first heading
                    .filter(h => h.level <= 6) // filter headings based on level
                    .map(h =>
                    {
                        // Determine indentation based on heading level
                        let indent        = " ".repeat(h.level);
                        var file_name     = p.file.name;
                        
                        // remove backticks and tag symbols
                        var file_head     = h.heading
                        var file_head     = file_head.replace(/`/g, '');
                        var file_head     = file_head.replace(/#/g, '');
                        var file_title    = h.heading.split('#')[0];

                        let objLink       = "[[" + file_name + "#" + file_head + "|" + file_title + "]]";

                        if ( h.level == 1 )
                            return indent + "- <sub>" + objLink + "</sub>";
                        else if ( h.level == 2 )
                            return indent + " - <sub>" + objLink + "</sub>";
                        else if ( h.level == 3 )
                            return indent + "  - <sub>" + objLink + "</sub>";
                        else if ( h.level == 4 )
                            return indent + "   - <sub>" + objLink + "</sub>";
                        else if ( h.level == 5 )
                            return indent + "    - <sub>" + objLink + "</sub>";
                        else if ( h.level == 6 )
                            return indent + "     - <sub>" + objLink + "</sub>";
                        else
                            return indent + "- " + objLink;
                    })
                    .join("\n") // Join the formatted headings with newlines
                    
                dv.el("div", houtput); // Display the formatted headings as a div
                dv.el("div", "<br />"); // insert line-break between sections
            }
        }
    });
```
````

<br />

### Example

![ASGH81Y](https://github.com/Aetherinox/obsidian-tableofcontents/assets/118329232/b0a7278e-9281-4db0-9bca-766a959bfe33)

<br /><br />

---

<br /><br />

## Notes
This is a very basic script. I wanted an auto-updating snippet where I could edit my vault notes and the script would automatically update the structure itself. Feel free to play with the code and make something better. If you want to share it, I'll add it here with credits.
