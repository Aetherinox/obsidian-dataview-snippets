# Obsidian: Table of Contents Snippet
These snippets require you to download a copy of [Obsidian.md](obsidian.md/)

<br /><br />

## About
This is a very simple **Table of Contents / TOC** script that I use in my Obsidian docs which auto-generates a list of headers and links associated to the sub-pages that you have created in your vault.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |
| [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) | ![GitHub release](https://img.shields.io/github/v/release/LostPaul/obsidian-folder-notes?label=v&color=ba0f56) |

<br />

Select which version you want to install:

| Version | Desc |
| --- | --- |
| [Version 1](#version-1) | Requires [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) + [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) |
| [Version 2](#version-2) | Requires [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) only |

<br /><br />

## Version 1
This version requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) and [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes) to run.

<br />

### Usage
- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Install [Folder Notes Plugin](https://github.com/LostPaul/obsidian-folder-notes)
- Right-click on a folder, select `Folder Note Commands` and select `Create folder note`
- Copy the code below and paste in Obsidian folder note.

<br />

````javascript
```dataviewjs

let count             = 0;
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

        count++;
    });

    if (count === 0)
    {
        const rootNode = dv.el("div", "No results", { cls: "toc-results-none" });
        rootNode.setAttribute("style", "text-align:center;");
    }
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
This version requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) to run.
Code should be implemented directly at the top of each note in order to outline all the separate headers that exist on that same page.

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

### Display pages as regular list and not indented
By default, all headers belonging to a page display as an unordered list such as:

```
1. Page Title
  - H2 Header
    - H3 Header
      - H4 Header
        - H5 Header
          - H6 Header
```

If you wish to have all pages show as a normal list with no indentation depending on the header type such as:

```
1. Page Title
H2 Header
H3 Header
H4 Header
H5 Header
H6 Header
```

Then locate the code displayed below:

```javascript
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
```

And change it to:
```javascript
if ( h.level == 1 )
    return indent + "<sub>" + objLink + "</sub>";
else if ( h.level == 2 )
    return indent + "<sub>" + objLink + "</sub>";
else if ( h.level == 3 )
    return indent + "<sub>" + objLink + "</sub>";
else if ( h.level == 4 )
    return indent + "<sub>" + objLink + "</sub>";
else if ( h.level == 5 )
    return indent + "<sub>" + objLink + "</sub>";
else if ( h.level == 6 )
    return indent + "<sub>" + objLink + "</sub>";
else
    return indent + "" + objLink;
```
