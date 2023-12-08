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

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Alphabetized%20List%201/images/example_1.gif"></p>

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

var html            = "";
let arrABC          = [];
let arrPages        = dv.pages( "" )
.forEach( p =>
{
    const file          = p.file
    const file_path     = file.path;
    const file_name     = file.name;
    const file_label    = file.frontmatter.name || file.frontmatter.title || file.frontmatter.alias || file_name;

    const letter        = file_label.charAt( 0 ).toUpperCase( );
    let index           = arrABC.findIndex( ( item ) => item.name === letter );

    if ( index === -1 )
        arrABC.push( { name: letter, pages: [ { name: file_name, label: file_label, path: file_path } ] } );
    else
    {
        var item        = arrABC.find( item => item.name == letter );
        let arr         = item.pages;

        arr.push( { name: file_name, label: file_label, path: file_path } );
    }

    arrABC.sort( ( a, b ) => a.name.localeCompare( b.name ) )
});

dv.list(
    dv.array( arrABC )
        .forEach( obj =>
        {
            const arrPages          = obj.pages;

            html += "\n<details><summary>" + obj.name + "</summary>\n\n";

            Promise.all( arrPages.map( async ( pages ) =>
            {
                const page_path     = pages.path;
                const page_name     = pages.name;
                const page_label    = pages.label;

                const file_link     = dv.fileLink( page_path, false, page_label );
                
                html += "- " + file_link + "\n";
            }
            ));

            html += "</details>\n";

        })

)

const divClose 		= dv.el( 'div', html, { container: dv.container, cls: 'atx-alv1-close' } );

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

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Alphabetized%20List%201/images/install_1.gif"></p>

<br />

Create a new file named whatever (`alphabetizedlist_v1.css` in our example).

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Alphabetized%20List%201/images/install_2.png"></p>

<br />

Copy the code below and paste it into the new `alphabetizedlist_v1.css` file which should be in `YourVaultName/.obsidian/snippets/alphabetizedlist_v1.css`

<br />

```css
/* @settings
name: ☁️ Alphabetized List - Version 1
id: atx-alv1
settings:
-
    id:                     atx-alv1-cat-general
    title:                  '1. Appearance'
    description:            'General appearance settings'
    type:                   heading
    level:                  1
    collapsed:              true


-
        id:                 atx-alv1-general-about
        title:              ''
        description:        "<br /><br /><h1>General</h1>General settings not categorized anywhere else."
        type:               info-text
        markdown:           true
-
        id:                 atx-alv1-general-container-padding
        title:              'Container Padding'
        description:        'Amount of padding between edge of tags and note width'
        type:               variable-number-slider
        default:            20
        format:             px
        min:                0
        max:                50
        step:               1
-
        id:                 atx-alv1-general-line-spacing
        title:              'Height between tag on each row'
        description:        'Font size for header description text.'
        type:               variable-number-slider
        default:            15
        format:             px
        min:                0
        max:                30
        step:               1







-
        id:                 atx-alv1-tags-about
        title:              ''
        description:        "<br /><br /><h1>Tags</h1>Settings for customizing tags."
        type:               info-text
        markdown:           true
-
        id:                 atx-alv1-tags-font-face
        title:              'Font Face'
        description:        'Font for tags'
        type:               variable-text
        default:            'sans-serif'
-
        id:                 atx-alv1-tags-font-clr-h
        title:              'Font Color (Hover)'
        description:        'Color of tag text when tag is hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'
-
        id:                 atx-alv1-tags-font-weight-n
        title:              'Font Weight (Normal)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-alv1-tags-font-weight-h
        title:              'Font Weight (Hover)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-alv1-tags-spacing-internal
        title:              'Spacing (Internal)'
        description:        'Spacing between left and right of tag text and edge of each container'
        type:               variable-number-slider
        default:            5
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-alv1-tags-spacing-external
        title:              'Spacing (External)'
        description:        'Spacing between each container box'
        type:               variable-number-slider
        default:            7
        format:             px
        min:                0
        max:                30
        step:               1


-
        id:                 atx-alv1-counter-about
        title:              ''
        description:        "<br /><br /><h1>Counter</h1>Settings for tag indicator count at top left of each tag when hovered."
        type:               info-text
        markdown:           true
-
        id:                 atx-alv1-counter-bg-clr
        title:              'Background Color'
        description:        'Background color for circle'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#424242'
        default-dark:       '#424242'
-
        id:                 atx-alv1-counter-bg-size
        title:              'Circle Size'
        description:        'Size of counter circle'
        type:               variable-number-slider
        default:            18
        format:             px
        min:                5
        max:                30
        step:               1
-
        id:                 atx-alv1-counter-font-face
        title:              'Font Face'
        description:        'Fonts except from the code texts'
        type:               variable-text
        default:            'sans-serif'
-
        id:                 atx-alv1-counter-font-clr-n
        title:              'Font Color'
        description:        'Color of tag text when tag is hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'
-
        id:                 atx-alv1-counter-font-size
        title:              'Font Size'
        description:        'Font size for counter'
        type:               variable-number-slider
        default:            10
        format:             px
        min:                5
        max:                30
        step:               1
-
        id:                 atx-alv1-counter-font-weight
        title:              'Font Weight'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal


-


    id:                     atx-alv1-cat-anim
    title:                  '2. Animations'
    description:            'Animation settings'
    type:                   heading
    level:                  1
    collapsed:              true
-
        id:                 atx-alv1-cat-anim-1
        title:              'Glow Animation'
        description:        'Play glow animation when hovering tag'
        type:               class-select
        allowEmpty:         false
        default:            anim-1-enabled
        options:
        -
            label: Enabled
            value: anim-1-enabled
        -
            label: Disabled
            value: anim-1-disabled
-
        id:                 atx-alv1-cat-anim-2
        title:              'Scale Animation'
        description:        'Scale text animation when hovering tag'
        type:               class-select
        allowEmpty:         false
        default:            anim-2-enabled
        options:
        -
            label: Enabled
            value: anim-2-enabled
        -
            label: Disabled
            value: anim-2-disabled
-
        id:                 atx-alv1-cat-anim-3
        title:              'Fade In-Out Effect'
        description:        'Tags fading in and out on hover'
        type:               class-select
        allowEmpty:         false
        default:            anim-3-enabled
        options:
        -
            label: Enabled
            value: anim-3-enabled
        -
            label: Disabled
            value: anim-3-disabled
-


    id:                     atx-alv1-cat-support
    title:                  '3. Support'
    description:            'Links associated to this snippet'
    type:                   heading
    level:                  1
    collapsed:              true
-
    id:                     atx-alv1-support-updates
    title:                  View Updates
    description:            "[https://github.com/Aetherinox/obsidian-dataview-snippets](https://github.com/Aetherinox/obsidian-dataview-snippets)"
    type:                   info-text
    markdown:               true
-

*/

    /*
        Import
    */

        @import url( http://fonts.googleapis.com/css?family=Open+Sans );

    /*
        @patch  : Blue Topaz Theme

        original Blue Topaz theme overrides animations to links.
        set transition to none and define our animations further down.
    */

        :is( .markdown-preview-view,.markdown-rendered ) a:hover
        {
            transition                              : none;
        }

    /*
        Snippet: Alphabetized List - Version 1
    */

        body
        {
            --atx-alv1-anim-1-glow:                     atx-alv1-anim-glow;
            --atx-alv1-anim-2-in:                       atx-alv1-anim-scale-in;
            --atx-alv1-anim-2-out:                      atx-alv1-anim-scale-out;
            --atx-alv1-anim-2-scale:                    scale( 1.3 );
            --atx-alv1-anim-3-opacity:                  0.5;

            --atx-alv1-general-line-spacing:            15px;
            --atx-alv1-general-container-padding:       20px;

            --atx-alv1-tags-font-face:                  sans-serif;
            --atx-alv1-tags-font-weight-n:              normal;
            --atx-alv1-tags-font-weight-h:              normal;
            --atx-alv1-tags-spacing-internal:           5px;
            --atx-alv1-tags-spacing-external:           7px;

            --atx-alv1-counter-bg-clr:                  #424242;
            --atx-alv1-counter-bg-size:                 18px;
            --atx-alv1-counter-font-face:               sans-serif;
            --atx-alv1-counter-font-clr-n:              #FFFFFF;
            --atx-alv1-counter-font-size:               10px;
            --atx-alv1-counter-font-weight:             normal;
        }

    /*
        Settings > Animations
    */

        body.theme-light.anim-1-disabled,
        body.theme-dark.anim-1-disabled
        {
            --atx-alv1-anim-1-glow      : none !important;
        }
        
        body.theme-light.anim-1-enabled,
        body.theme-dark.anim-1-enabled
        {
            --atx-alv1-anim-1-glow      : atx-alv1-anim-glow;
        }

    /*
        animation: glow
    */

        @keyframes atx-alv1-anim-glow
        {
            from
            {
                box-shadow             : 0 0 30px rgba(230, 0, 115, 0.05), 0 0 40px rgba(230, 0, 115, 0.29), 0 0 50px rgba(230, 0, 115, 0.05), 0 0 60px rgba(230, 0, 115, 0.05), 0 0 70px rgba(230, 0, 115, 0.29), 0 0 80px rgba(230, 0, 115, 0.39), 0 0 90px rgba(230, 0, 115, 0.05);
            }
            to
            {
                box-shadow             : 0 10px 0px rgba(230, 0, 115, 0.05), 0 0 10px rgba(230, 0, 115, 0.05), 0 0 20px rgba(230, 0, 115, 0.19), 0 0 30px rgba(230, 0, 115, 0.05), 0 0 40px rgba(230, 0, 115, 0.05), 0 0 50px rgba(230, 0, 115, 0.39), 0 0 60px rgba(230, 0, 115, 0.05);
            }
        }

    /*
        animation: scale
    */

        @keyframes atx-alv1-anim-scale-in
        {
            0%
            {
                transform               : scale( 1 );
            }
            100%
            {
                transform               : scale( 1.3 );
            }
        }
          
        @keyframes atx-alv1-anim-scale-out
        {
            0%
            {
                transform               : scale( 1.3 );
            }
            100%
            {
                transform               : scale( 1 );
            }
        }

    /*
        animation: blink
    */
        
        @keyframes atx-alv1-anim-blink
        {
            0%
            {
                opacity                 : 0.70;
            }
            50%
            {
                opacity                 : 1;
            }
            100%
            {
                opacity                 : 0.70;
            }
          }


    /*
        Dataview parent div
    */

        .atx-alv1-dataview
        {
            border-radius               : 6px;
            border                      : 1px dashed #831b3d;
            width                       : var( --atx-blv1-bg-width );
            padding                     : 5px;
            margin                      : 0 auto;
        }

    /*
        Tab > Padding
    */

        details
        {
            padding-bottom              : 4px;
        }

        details[ open ]
        {
            margin-bottom               : 4px;
        }

    /*
        Tab > Open > Container padding
    */

        details[ open ] summary ~ *
        {
            padding-top                 : 10px;
            padding-left                : 40px;
            padding-bottom              : 10px;
        }

    /*
        Tab > Normal
    */

        details summary
        {
            cursor                      : pointer;
            transition                  : all ease-in-out 0.3s;
            font-weight                 : bold;
            background-color            : #1b1b1b;
            user-select                 : none;
            font-size                   : 22px !important;
        }

    /*
        Tab > Hover
    */

        details summary:hover
        {
            background-color            : #8f2045;
            animation                   : atx-alv1-anim-blink 1s infinite;
        }

    /*
        Tab > Closed
    */

        details > summary
        {
            list-style-image            : url( "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 448 512' width='15px' height='15px' fill='%23FFFFFF'%3E%3Cdefs%3E%3Cstyle%3E.fa-secondary%7Bopacity:.4%7D%3C/style%3E%3C/defs%3E%3Cpath class='fa-primary' d='M438.6 233.4c12.5 12.5 12.5 32.8 0 45.3l-160 160c-12.5 12.5-32.8 12.5-45.3 0s-12.5-32.8 0-45.3L370.7 256 233.4 118.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l160 160z'%3E%3C/path%3E%3Cpath class='fa-secondary' d='M338.7 224L32 224c-17.7 0-32 14.3-32 32s14.3 32 32 32l306.7 0 32-32-32-32z'%3E%3C/path%3E%3C/svg%3E" );
            padding-left                : 20px;
            padding-top                 : 4px;
        }

    /*
        Tab > Open
    */

        details[ open ] > summary
        {
            list-style-image            : url( "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 384 512' width='15px' height='15px' fill='%23FFFFFF'%3E%3Cdefs%3E%3Cstyle%3E.fa-secondary%7Bopacity:.4%7D%3C/style%3E%3C/defs%3E%3Cpath class='fa-primary' d='M169.4 470.6c12.5 12.5 32.8 12.5 45.3 0l160-160c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 402.7 54.6 265.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l160 160z'%3E%3C/path%3E%3Cpath class='fa-secondary' d='M160 370.7V64c0-17.7 14.3-32 32-32s32 14.3 32 32V370.7l-32 32-32-32z'%3E%3C/path%3E%3C/svg%3E" );
            padding-left                : 20px;
            padding-top                 : 4px;
        }

    /*
        Tab > Marker
    */

        details summary::marker
        {
            padding-left                : 10px;
            margin-left                 : 20px;
        }

    /*
        Tab > Arrow [old] > Closed

        this was utilized when the tab arrow icon was originally defined using a background image.
        keep this for now in case its needed.
    */

        details summary::before
        { 
            content                     : ""; 
            padding-left                : 8px;
            background-position         : 4px 4px;
            background-repeat           : no-repeat;
        }

    /*
        Tab > Arrow [old] > Open

        this was utilized when the tab arrow icon was originally defined using a background image.
        keep this for now in case its needed.
    */

        details[ open ] summary::before
        {
            content                     : ""; 
            padding-left                : 8px;
            background-position         : 4px 4px;
            background-repeat           : no-repeat;
        }

        details[ open ]
        {
            padding-bottom              : 10px;
        }
        
        details[ open ] summary
        {
            border-bottom               : 1px dashed #f73877;
            user-select                 : none;
            margin-bottom               : 5px;
            background-color            : #7c1236;
        }

        details[ open ] ul
        {
            background-color            : #272727;
            margin-block-start          : -2px;
            margin-block-end            : -10px;
            animation-name              : atx-alv1-anim-glow !important;
            animation-duration          : 1s;
            animation-timing-function   : ease-in-out;
            animation-iteration-count   : infinite;
            animation-direction         : alternate;
        }



        
        details[ open ] ul li a.internal-link
        {
            color                       : #f73877 !important;
        }

    /*
        List
    */

        details ul li a.internal-link:hover
        {
            background                  : none;
            color                       : #FFF !important;
        }
```

<br />
<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `alphabetizedlist_v1.css`.

<br />
<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Alphabetized%20List%201/images/install_3.gif"></p>

<br />

You should see a list of pages associated to your vault.

<br />

This snippet supports modifying the CSS values using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how the tags in this snippet look:
- Open `Obsidian Settings`
- Install the `Style Settings` plugin
- Select `Style Settings` config panel under `Community Plugins`.
- Click the tab `Alphabetized List - Version 1`
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
This script supports `Frontmatter` / `Metadata` titles. The name of each page displayed has several ways of being defined, and supports **frontmatter**.

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
- Click the tab `Alphabetized List - Version 1`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Alphabetized%20List%201/images/example_5.gif"></p>

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />