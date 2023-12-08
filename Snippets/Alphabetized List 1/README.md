# Obsidian: Alphabetized List - Version 1 <!-- omit from toc -->
- This snippet requires the program [Obsidian.md](obsidian.md/)
- This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- This snippet has optional support for [Style Settings Plugin](https://github.com/mgmeyers/obsidian-style-settings)

---

<br />

## Table of Contents <!-- omit from toc -->

- [About](#about)
  - [Previews](#previews)
- [Install](#install)
  - [Javascript](#javascript)
    - [Normal Version](#normal-version)
    - [Minified Version](#minified-version)
  - [CSS](#css)
- [Customization](#customization)
  - [Page Titles / Frontmatter Support](#page-titles--frontmatter-support)
  - [Change Appearance / CSS](#change-appearance--css)

<br />

---

<br /><br />

## About
The `Alphabetized List - Version 1` snippet fetches a list of every page which exists in your vault, and automatically sorts them in a A, B, C collapsible structure.

<br />

Each letter of the alphabet is clickable, and displays a list of pages in your vault starting with that letter.

<br />

This snippet supports `Frontmatter` values for page names. For more information on this, view the customization section: [Page Titles / Frontmatter Support](#page-titles--frontmatter-support).

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |


<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />

---

<br /><br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_1.gif"></p>

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />

---

<br /><br />

## Install

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- View the [Javascript](#javascript) section below, and copy the [Normal](#normal-version) or [Minified](#minified-version) version of the code and paste it into your Obsidian note.
- View the [CSS](#css) section below, and copy the provided CSS, then create a new `.css` snippet and paste the copied code.
- Enable the new CSS snippet in your `Obsidian Settings` under `Appearance`.
- More detailed instructions below.

<br /><br />

### Javascript
Pick **One** of the versions below.
<small>The features are the same, just the code is structured differently.</small>
1. [Normal Version](#normal-version)<br /><small>Much easier to read the code</small>
2. [Minified Version](#minified-version)<br /><small>Much easier to paste</small>

<br />

#### Normal Version
This version is much easier to read the code. It includes formatting and comments.

<br />

````shell
```dataviewjs
dv.container.className += ' atx-alv1-dataview'

let arrABC  	= [];
let arrPages 	= dv.pages( "" )
.forEach( p =>
{
    const file  			= p.file
    const file_path 		= file.path;
    const file_name			= file.name;
    const file_label 		= file.frontmatter.name || file.frontmatter.title || file.frontmatter.alias || file_name;

    const letter 			= file_label.charAt( 0 ).toUpperCase( );
    let index 				= arrABC.findIndex( ( item ) => item.name === letter );

    if ( index === -1 )
        arrABC.push( { name: letter, pages: [ { name: file_name, label: file_label, path: file_path } ] } );
    else
    {
        var item 	= arrABC.find( item => item.name == letter );
        let arr 	= item.pages;

        arr.push( { name: file_name, label: file_label, path: file_path } );
    }

    arrABC.sort( ( a, b ) => a.name.localeCompare( b.name ) )
});

var sethtml = "";

dv.list(
    dv.array( arrABC )
        .forEach( obj =>
        {
            const arrPages 			= obj.pages;

            sethtml += "\n<details><summary>" + obj.name + "</summary>\n\n";

            Promise.all( arrPages.map( async ( pages ) =>
            {
                const page_path 	= pages.path;
                const page_name 	= pages.name;
                const page_label 	= pages.label;

                const file_link     = dv.fileLink( page_path, false, page_label );
                
                sethtml += "- " + file_link + "\n";
            }
            ));

            sethtml += "</details>\n";

        })

)

const divClose 		= dv.el( 'div', sethtml, { container: dv.container, cls: 'atx-alv1-s' } );

```
````

<br />

#### Minified Version
This version only formats the settings. All other formatting and comments are removed.

<br />

````shell
```dataviewjs
    Not Available Yet
```
````

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />

---

<br /><br />

### CSS
Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/install_1.gif"></p>

<br />

Create a new file named whatever (`badlinks_v1.css` in our example).

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/install_2.png"></p>

<br />

Copy the code below and paste it into the new `badlinks_v1.css` file which should be in `YourVaultName/.obsidian/snippets/badlinks_v1.css`

<br />

```css
    Not Available Yet
```

<br />
<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `badlinks_v1.css`.

<br />
<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/install_3.gif"></p>

<br />

You should see a list of pages associated to your vault.

<br />

This snippet supports modifying the CSS values using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how the tags in this snippet look:
- Open `Obsidian Settings`
- Install the `Style Settings` plugin
- Select `Style Settings` config panel under `Community Plugins`.
- Click the tab `Bad Links - Version 1`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_4.gif"></p>

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />

---

<br /><br />

## Customization
The section below explains how to customize this snippet.

<br />
<br />

### Page Titles / Frontmatter Support
This script supports `Frontmatter` / `Metadata` titles. When one of your notes includes a broken link, the name of the note will display in the Bad Links snippet. The name of each page displayed has several ways of being defined, and supports **frontmatter**.

<br />

The name that displays for each page will have the following priority:
1. `frontmatter.name`
2. `frontmatter.title`
3. `frontmatter.alias`
4. `filename.Name`

<br />

If none of the above frontmatter values are specified, the normal file name of the page will be used.

<br />

Based on the priority list above, if you provide both a frontmatter `name` AND `title`, the frontmatter **name** will be used first. 

<br />

If you provide a frontmatter `title` and frontmatter `alias`, then the frontmatter **title** will be used.

<br />

To define frontmatter values, add the following to the very top of your page:

```markdown
---
title:   "Your Page Title"
---
```

<br />

You cannot have any blank lines above the first `---`.

<br />

If you do not specify `title`, `name`, or `alias` in your frontmatter, the normal page filename will be used.

<br />
<br />

### Change Appearance / CSS
This snippet supports tweaking the look and feel of the cloud & tags using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how this snippet looks:
- Open `Obsidian Settings`
- Install the `Style Settings` plugin
- Select `Style Settings` config panel under `Community Plugins`.
- Click the tab `Bad Links - Version 1`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_5.gif"></p>

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />