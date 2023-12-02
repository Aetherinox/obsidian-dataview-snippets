# Obsidian: Page Cloud : Version 1 <!-- omit from toc -->
- This snippet requires the program [Obsidian.md](obsidian.md/)
- This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- This snippet has optional support for [Style Settings Plugin](https://github.com/mgmeyers/obsidian-style-settings)

---

<br />

## Table of Contents <!-- omit from toc -->

- [About](#about)
  - [Previews](#previews)
- [Install](#install)
  - [Normal Version](#normal-version)
  - [Minified Version](#minified-version)
- [Customization](#customization)
  - [Page Titles / Frontmatter Support](#page-titles--frontmatter-support)
  - [Filtering Folders and Pages](#filtering-folders-and-pages)
    - [Exclude Folders](#exclude-folders)
    - [Exclude Page](#exclude-page)
  - [Sort Order](#sort-order)
  - [Font Size](#font-size)
  - [Font Weight](#font-weight)
  - [Cloud Color Generation](#cloud-color-generation)
  - [Change Appearance / CSS](#change-appearance--css)

<br />

---

<br /><br />

## About
The `Page Cloud - Version 1` snippet will fetch a list of all the pages that exist within your vault and list them in a cloud format. Certain pages will display different font sizes and colors depending on how populated the page is with contents.

<br />

This snippet supports `Frontmatter` values for the page names. For more information on this, view the customization section: [Page Titles / Frontmatter Support](#page-titles--frontmatter-support).

<br />

> [!WARNING]
> This snippet may be slow loading depending on the number of pages you have available in your vault. At the time of developing this, it was tested on a vault with over 400 pages / notes. Which took 1-3 seconds to load the cloud.
>
> Unfortunately this is mostly limited by the performance of Dataview. Larger projects should either use a Cloud plugin solution, or increase the dataview refresh interval. The refresh interval `100` was used in this test.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |


<br /><br />

**[`^        back to top        ^`](#table-of-contents-)**

<br /><br />

---

<br /><br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/example_1.gif"></p>

<br /><br />

**[`^        back to top        ^`](#table-of-contents-)**

<br /><br />

---

<br /><br />

## Install

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Pick one of the two versions below, copy the code, and paste it in your Obsidian note.

<br />

Pick **One**.
<br />
<small>The features are the same, just the code is structured differently.</small>
1. [Normal Version](#normal-version)<br /><small>Much easier to read the code</small>
2. [Minified Version](#minified-version)<br /><small>Much easier to paste</small>

<br />

### Normal Version
This version is much easier to read the code. It includes formatting and comments.

<br />

````shell
```dataviewjs
/*
    Snippet: Page Cloud - Version 1

    This snippet requires the Dataview Plugin
        https://github.com/blacksmithgu/obsidian-dataview

    View settings at:
        https://github.com/Aetherinox/obsidian-dataview-snippets/tree/main/Snippets/Page%20Cloud%201

    Creates a cloud which shows all of the pages that exist in your vault.
*/

/*
    Settings
*/

const QueryStr          = `""`;
const QueryFiles        = dv.pages( QueryStr );

const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.5;
const minFontSize       = 12;
const maxFontSize       = 32;
const arrColors         = [];

/*
    Generate 40 colors
*/

for ( let i = 0; i < 40; i++ )
{

    let R           = Math.floor( ( Math.random( ) * 100 ) + 100 );
    let G           = Math.floor( ( Math.random( ) * 100 ) + 100 );
    let B           = Math.floor( ( Math.random( ) * 100 ) + 100 );
    
    let rgb         = ( R << 16 ) + ( G << 8 ) + B;
    let itemColor   = `#${rgb.toString( 16 )}`;

    arrColors.push( itemColor );
}

/*
    Get all backlinks
*/

async function QueryBacklinks( q )
{
    const file = q.split( '/' ).pop( ).split( "." ).slice( 0, -1 ).join( "." );
    return dv.query(
    `
    LIST
    FROM [[${file}]] AND ${QueryStr}
    `
    );
}

/*
    Get number of words in each file
*/

async function QueryWordcount( q )
{
    const fs            = require( 'fs' );
    const path          = require( 'path' );
    const text          = fs.readFileSync( path.join( app.vault.adapter.basePath, q ), 'utf-8' );
    const pattern       = /---[\s\S]*?---|```[\s\S]*?```|\$[\s\S]*?\$|\$\$[\s\S]*?\$\$/g;
    const cleanedText   = text.replace( pattern, '' );
    const matchText     = cleanedText.match( /\S+/g );

    if ( !matchText )
        return 0;
    
    return matchText.length;
}

/*
    Calculate font text size which is determined by number of backlinks
    and number of words available.
*/

function Generate_FontSize( backlinks, wordCount )
{
    const calcFontSize = Math.sqrt( ( backlinks * weightBacklinks ) + ( wordCount * weightWordCount ) ) * 2.5;
    return Math.round( ( calcFontSize / 100 ) * ( maxFontSize - minFontSize ) + minFontSize );
}

/*
    Generate font color
*/

function Generate_Color( tagName )
{
    if ( tagName == null ) { return "#FFFFFF"; }

    const tagWords      = tagName.split( /\W+/g );
    const colorIndex    = tagWords.reduce( ( total, word ) => total + word.charCodeAt( 0 ), 0 ) % Object.keys( arrColors ).length;

    return arrColors[ Object.keys( arrColors )[ colorIndex ] ];
}

/*
    Sort > DESC / ASC

    alphabetize array results
*/

function Sort_DESC( arr )
{
    arr.sort( ( a, b ) => a.id.localeCompare( b.id ) )
    return arr;
}

function Sort_ASC( arr )
{
    arr.sort( ( a, b ) => b.id.localeCompare( a.id ) )
    return arr;
}

/*
    Sort > Shuffle

    randomized array results
*/

function Sort_Shuffle( arr )
{
    for ( let i = arr.length - 1; i > 0; i-- )
    {
        const j                 = Math.floor( Math.random( ) * ( i + 1 ) );
        [ arr[ i ], arr[ j ] ]  = [ arr[ j ], arr[ i ] ];
    }

    return arr;
}

/*
    Create page cloud
*/

const CreatePageCloud = async ( ) =>
{
    const files     = new Map( );

    /*
        Add all .md files to the tags map with their backlinks and word count
    */

    Promise.all( QueryFiles.map( async ( f ) =>
    {
        const file              = f.file
        const blq               = QueryBacklinks( file.path )
        const wcq               = QueryWordcount( file.path )

        const fileInfo          = { backlinks: 0, wordCount: 0 };
        const res               = await blq;
        fileInfo.backlinks      = res.value.values.length;
        const wc                = await wcq;
        fileInfo.wordCount      = wc;

        files.set( file, fileInfo );

    })).then( ( ) =>
    {
        const data = []

        /*
            Calculate font size and font color.
        */

        files.forEach( ( fileInfo, fileName ) =>
        {
            if ( fileName == null )
                return;

            var file_name_fm    = fileName.frontmatter.name || fileName.frontmatter.title || fileName.frontmatter.alias;
            var file_name       = fileName.name

            if (file_name_fm)
                file_name       = file_name_fm;

            const fontSize      = Generate_FontSize( fileInfo.backlinks, fileInfo.wordCount );
            const color         = Generate_Color( fileName.name );
            const length        = 0;

            data.push( { name: file_name, id: fileName.name, size: fileName.size, path: fileName.path, fontSize, color } );
        });

        /*
            Sorting functions
        */

        const sortOptions =
        {
            1: 'Sort_DESC',
            2: 'Sort_ASC',
            3: 'Sort_Shuffle',
        };

        let funcSort = sortOptions[ sortOption ]

        if ( funcSort === undefined )
            funcSort = sortOptions[ 1 ]

        /*
            Return results
        */

        return eval( funcSort )( data ).map( ( tag ) =>
        {
            return `<span class="page-cloud-v1-item"><a class="page-cloud-v1-link" href="obsidian://open?file=${encodeURIComponent(tag.id)}" style="font-size:${tag.fontSize}px; color: ${tag.color};">${tag.name}</a></span>`;
        }
    ).join( "" );
    } ).then( res => dv.paragraph( res ) )
    .catch( error =>
    {
        console.error( "Error: " + error );
    } );
}

CreatePageCloud( )
```
````

<br />

### Minified Version
This version only formats the settings. All other formatting and comments are removed.

<br />

````shell
```dataviewjs
const QueryStr          = `""`;
const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.5;
const minFontSize       = 12;
const maxFontSize       = 32;

arrColors=[],QueryFiles=dv.pages(QueryStr);for(let i=0;i<40;i++){let itemColor=`#${((Math.floor(100*Math.random()+100)<<16)+(Math.floor(100*Math.random()+100)<<8)+Math.floor(100*Math.random()+100)).toString(16)}`;arrColors.push(itemColor)}async function QueryBacklinks(e){let t=e.split("/").pop().split(".").slice(0,-1).join(".");return dv.query(`LIST FROM [[${t}]] AND ${QueryStr}`)}async function QueryWordcount(e){let t=require("fs"),r=require("path"),o=t.readFileSync(r.join(app.vault.adapter.basePath,e),"utf-8"),n=/---[\s\S]*?---|```[\s\S]*?```|\$[\s\S]*?\$|\$\$[\s\S]*?\$\$/g,a=o.replace(n,""),l=a.match(/\S+/g);return l?l.length:0}function Generate_FontSize(e,t){let r=2.5*Math.sqrt(e*weightBacklinks+t*weightWordCount);return Math.round(r/100*(maxFontSize-minFontSize)+minFontSize)}function Generate_Color(e){if(null==e)return"#FFFFFF";let t=e.split(/\W+/g),r=t.reduce((e,t)=>e+t.charCodeAt(0),0)%Object.keys(arrColors).length;return arrColors[Object.keys(arrColors)[r]]}function Sort_DESC(e){return e.sort((e,t)=>e.id.localeCompare(t.id)),e}function Sort_ASC(e){return e.sort((e,t)=>t.id.localeCompare(e.id)),e}function Sort_Shuffle(e){for(let t=e.length-1;t>0;t--){let r=Math.floor(Math.random()*(t+1));[e[t],e[r]]=[e[r],e[t]]}return e}const CreatePageCloud=async()=>{let files=new Map;Promise.all(QueryFiles.map(async e=>{let t=e.file,r=QueryBacklinks(t.path),o=QueryWordcount(t.path),n={backlinks:0,wordCount:0},a=await r;n.backlinks=a.value.values.length;let l=await o;n.wordCount=l,files.set(t,n)})).then(()=>{let data=[];files.forEach((e,t)=>{if(null==t)return;var r=t.frontmatter.name||t.frontmatter.title||t.frontmatter.alias,o=t.name;r&&(o=r);let n=Generate_FontSize(e.backlinks,e.wordCount),a=Generate_Color(t.name);data.push({name:o,id:t.name,size:t.size,path:t.path,fontSize:n,color:a})});let sortOptions={1:"Sort_DESC",2:"Sort_ASC",3:"Sort_Shuffle"},funcSort=sortOptions[sortOption];return void 0===funcSort&&(funcSort=sortOptions[1]),eval(funcSort)(data).map(e=>`<span class="page-cloud-v1-item"><a class="page-cloud-v1-link" href="obsidian://open?file=${encodeURIComponent(e.id)}" style="font-size:${e.fontSize}px; color: ${e.color};">${e.name}</a></span>`).join("")}).then(e=>dv.paragraph(e)).catch(e=>{console.error("Error: "+e)})};CreatePageCloud();
```
````

<br />
<br />

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_1.gif"></p>

<br />

Create a new file named whatever (`page_cloud_v1.css` in our example).

Copy the code below and paste it into the new `page_cloud_v1.css` file which should be in `YourVaultName/.obsidian/snippets/page_cloud_v1.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_2.png"></p>

<br />

```css
/* @settings
name: ☁️ Page Cloud - Version 1
id: atx-pcv1
settings:
-
    id:                     atx-pcv1-cat-general
    title:                  '1. Appearance'
    description:            'General appearance settings'
    type:                   heading
    level:                  1
    collapsed:              true
-
        id:                 atx-pcv1-bg-clr-n
        title:              'Background Color (Normal)'
        description:        'Background color for each page in cloud.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#252525'
        default-dark:       '#252525'
-
        id:                 atx-pcv1-bg-clr-h
        title:              'Background Color (Hover)'
        description:        'Background color for each page in cloud when hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#810d3d'
        default-dark:       '#810d3d'
-
        id:                 atx-pcv1-bg-border-clr-n
        title:              'Border Color (Normal)'
        description:        'Border color for each page in cloud.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#353535'
        default-dark:       '#353535'
-
        id:                 atx-pcv1-bg-border-clr-h
        title:              'Border Color (Hover)'
        description:        'Border color for each page in cloud when hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#dd2a74'
        default-dark:       '#dd2a74'
-
        id:                 atx-pcv1-txt-clr-h
        title:              'Text Color (Hover)'
        description:        'Text color for each page in cloud when hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'
-
        id:                 atx-pcv1-txt-weight-n
        title:              'Font Weight (Normal)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-pcv1-txt-weight-h
        title:              'Font Weight (Hover)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-pcv1-txt-margin-vertical
        title:              'Tag: Margin (Vertical)'
        description:        'Vertical margin for each tag'
        type:               variable-number-slider
        default:            5
        format:             px
        min:                0
        max:                20
        step:               1
-
        id:                 atx-pcv1-txt-margin-horizontal
        title:              'Tag: Margin (Horizontal)'
        description:        'Horizontal margin for each tag'
        type:               variable-number-slider
        default:            7
        format:             px
        min:                0
        max:                20
        step:               1
-
        id:                 atx-pcv1-txt-padding-vertical
        title:              'Tag: Padding (Vertical)'
        description:        'Vertical padding for each tag'
        type:               variable-number-slider
        default:            4
        format:             px
        min:                0
        max:                20
        step:               1
-
        id:                 atx-pcv1-txt-padding-horizontal
        title:              'Tag: Padding (Horizontal)'
        description:        'Horizontal padding for each tag'
        type:               variable-number-slider
        default:            7
        format:             px
        min:                0
        max:                20
        step:               1
-
        id:                 atx-pcv1-txt-line-height
        title:              'Tag: Line Height'
        description:        'Line height between each row of page tags.'
        type:               variable-number-slider
        default:            30
        format:             px
        min:                0
        max:                40
        step:               1
-
    id:                     atx-pcv1-cat-anim
    title:                  '2. Animations'
    description:            'Animation settings'
    type:                   heading
    level:                  1
    collapsed:              true
-
        id:                 atx-pcv1-cat-anim-1
        title:              'Hover Animation 1'
        type:               class-select
        allowEmpty:         false
        default:            anim-1-pulse
        options:
        -
            label: None
            value: anim-1-disabled
        -
            label: Pulse
            value: anim-1-pulse
-
        id:                 atx-pcv1-cat-anim-2
        title:              'Hover Animation 2'
        type:               class-select
        allowEmpty:         false
        default:            anim-2-glow
        options:
        -
            label: None
            value: anim-2-disabled
        -
            label: Glow
            value: anim-2-glow
-
    id:                     atx-pcv1-bg-anim-clr-n
    title:                  'Glow Color'
    description:            'Glow color on page tag hover.'
    type:                   variable-themed-color
    opacity:                true
    format:                 rgb
    default-light:          'rgba(237, 29, 68, 0.19)'
    default-dark:           'rgba(237, 29, 68, 0.19)'
-
    id:                     atx-pcv1-cat-support
    title:                  '3. Support'
    description:            'Links associated to this snippet'
    type:                   heading
    level:                  1
    collapsed:              true
-
    id:                     atx-pcv1-support-updates
    title:                  View Updates
    description:            "[https://github.com/Aetherinox/obsidian-dataview-snippets](https://github.com/Aetherinox/obsidian-dataview-snippets)"
    type:                   info-text
    markdown:               true
-

*/

    /*
        animation: glow
    */

        @keyframes atx-pcv1-anim-glow-box
        {
            from
            {
                box-shadow:             0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n);
            }
            to
            {
                box-shadow:             0 0 10px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 15px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 20px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 25px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 30px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 35px var(--atx-pcv1-bg-anim-clr-n),
                                        0 0 40px var(--atx-pcv1-bg-anim-clr-n);
            }
        }

    /*
        animation: pulse
    */

        @keyframes atx-pcv1-anim-pulse
        {
            0%
            {
                transform:              scale(1);
                box-shadow:             0 0 0 0 rgba(0, 0, 0, 0.7);
            }

            50%
            {
                transform:              scale(1.07);
                box-shadow:             0 0 0 10px rgba(0, 0, 0, 0);
            }

            100%
            {
                transform:              scale(1);
                box-shadow:             0 0 0 0 rgba(0, 0, 0, 0);
            }
        }

    /*
        Snippet: Page Cloud - Version 1
    */

        body
        {
            --atx-pcv1-bg-clr-n:                    #252525;
            --atx-pcv1-bg-clr-h:                    #810d3d;
            --atx-pcv1-bg-border-clr-n:             #353535;
            --atx-pcv1-bg-border-clr-h:             #dd2a74;
            --atx-pcv1-txt-clr-h:                   #FFFFFF;
            --atx-pcv1-txt-margin-vertical:         5px;
            --atx-pcv1-txt-margin-horizontal:       7px;
            --atx-pcv1-txt-padding-vertical:        4px;
            --atx-pcv1-txt-padding-horizontal:      7px;
            --atx-pcv1-anim-1:                      atx-pcv1-anim-pulse;
            --atx-pcv1-anim-2:                      atx-pcv1-anim-glow-box;
            --atx-pcv1-txt-line-height:             30px;
            --atx-pcv1-txt-weight-n:                bold;
            --atx-pcv1-txt-weight-h:                normal;
            --atx-pcv1-bg-anim-clr-n:               rgba(237, 29, 68, 0.19);
        }

    /*
        Settings > Animations
    */

        body.theme-light.anim-1-disabled,
        body.theme-dark.anim-1-disabled
        {
            --atx-pcv1-anim-1:          none;
        }
        
        body.theme-light.anim-1-default,
        body.theme-dark.anim-1-default
        {
            --atx-pcv1-anim-1:          atx-pcv1-anim-pulse;
        }

        body.theme-light.anim-2-disabled,
        body.theme-dark.anim-2-disabled
        {
            --atx-pcv1-anim-2:          none;
        }
        
        body.theme-light.anim-2-default,
        body.theme-dark.anim-2-default
        {
            --atx-pcv1-anim-2:          atx-pcv1-anim-glow-box;
        }

        body.colorful-link-animation :is(.markdown-preview-view,.markdown-rendered) a:hover
        {
            animation:                  none !important; 
        }

    /*
        Tag Cloud > Item
    */

        .page-cloud-v1-item
        {
            background-color:           var(--atx-pcv1-bg-clr-n);
            margin-top:                 var(--atx-pcv1-txt-margin-vertical);
            margin-bottom:              var(--atx-pcv1-txt-margin-vertical);
            margin-left:                var(--atx-pcv1-txt-margin-horizontal);
            margin-right:               var(--atx-pcv1-txt-margin-horizontal);
            padding-top:                var(--atx-pcv1-txt-padding-vertical);
            padding-bottom:             var(--atx-pcv1-txt-padding-vertical);
            padding-left:               var(--atx-pcv1-txt-padding-horizontal);
            padding-right:              var(--atx-pcv1-txt-padding-horizontal);
            border:                     1px solid var(--atx-pcv1-bg-border-clr-n);
            border-radius:              6px;
            display:                    inline-block;
        }

        .page-cloud-v1-item:has( > a:hover )
        {
            background:                 var(--atx-pcv1-bg-clr-h);
            border:                     1px solid var(--atx-pcv1-bg-border-clr-h) !important;
            cursor:                     pointer;
            animation-name:             var(--atx-pcv1-anim-1), var(--atx-pcv1-anim-2) !important;
            animation-duration:         2s, 1s;
            animation-timing-function:  ease, ease-in-out;
            animation-iteration-count:  infinite, infinite;
            animation-direction:        normal, alternate;
        }

    /*
        Tag Cloud > Links
    */

        a.page-cloud-v1-link
        {
            line-height:                var(--atx-pcv1-txt-line-height);
            vertical-align:             middle;
            text-decoration:            none;
            font-weight:                var(--atx-pcv1-txt-weight-n);
            background:                 none;
        }

        a.page-cloud-v1-link:hover
        {
            color:                      var(--atx-pcv1-txt-clr-h) !important;
            background:                 none;
            font-weight:                var(--atx-pcv1-txt-weight-h);
        }
```

<br />
<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `page_cloud_v1.css`.

<br />
<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_3.gif"></p>

<br />

You should see a list of pages associated to your vault.

<br />

This snippet supports modifying the CSS values using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how the tags in this snippet look:
- Open Obsidian Settings
- Install the **Style Settings** plugin
- Select **Style Settings** config panel under **Community Plugins**.
- Click the tab `Page Cloud - Version 1`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_4.gif"></p>

<br /><br />

**[`^        back to top        ^`](#table-of-contents-)**

<br /><br />

---

<br /><br />

## Customization
The section below explains how to customize this snippet.

<br />
<br />

### Page Titles / Frontmatter Support
Each page within your vault will be displayed in a cloud structure. The name of each page displayed has several ways of being defined, and supports **frontmatter**.

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

### Filtering Folders and Pages
This snippet allows you to filter out folders and pages which will not be included in your list of generated pages that appear in your cloud. The syntax is the same as normal Dataview queries.

To filter out folders or pages, edit the following:

```javascript
const QueryStr = `""`;
```

<br />

#### Exclude Folders

To filter out or exclude the folder `"My Personal Stuff"`, and all notes in that folder; use the following:

```javascript
const QueryStr = `-"My Personal Stuff"`;
```

The folder name **must** be enclosed in quotation marks `"`. 

<br />

The `-` character represents an `exclude`, and must be used OUTSIDE the quotation marks.

<br />

To exclude multiple folders, add `AND` between each folder.

```javascript
const QueryStr = `-"My Personal Stuff" AND `-"Another Folder"`;
```

<br />
<br />

#### Exclude Page
To filter out a specific file or page, it is similar to the above, except you must provide the full path to the page, including the folder.

<br />

For example, if you have a folder named `Personal Stuff`, and a page inside called `My Pin Codes`, you can exclude it using:

```javascript
const QueryStr = `-"Personal Stuff/My PIN Codes"`;
```

<br />
<br />

### Sort Order
This snippet allows for two ways of sorting pages:
1. Alphabetically (Descending A-Z)
2. Alphabetically (Ascending Z-A)
3. Random / Shuffle

<br />

To change the sorting, edit the property:

```javascript
const sortOption = 1;
```

<br />
<br />

### Font Size
The font size of a listed page is determined by two things:
1. Amount of content that exists on a page
2. Number of times a page is called from another back

<br />

To limit the font sizes that are used, edit the two properties below:

```javascript
const minFontSize = 12;
const maxFontSize = 32;
```

<br />
<br />

### Font Weight
The font weight of a page is determined by two things:
1. Amount of content that exists on a page
2. Number of times a page is called from another back

<br />

To limit the font weight sizes used, edit the two properties below. The lower the value, the thinner the page name. Higher numbers will display more bold text. This setting also plays a role in the [Font Size](#font-size)

```javascript
const weightBacklinks = 0.1;
const weightWordCount = 0.3;
```

<br />
<br />

### Cloud Color Generation
As of `v1.3.0`, this snippet auto generates the colors that will be used for each page listed in the cloud. The color generation code will keep the color tones in the **pastel** range, and will exclude darker colors since the background box color is dark.

<br />

To modify the color range, edit the following code:

```javascript
let R = Math.floor( ( Math.random( ) * 100 ) + 100 );
let G = Math.floor( ( Math.random( ) * 100 ) + 100 );
let B = Math.floor( ( Math.random( ) * 100 ) + 100 );
```

<br />

For brighter colors, increase `100`. Don't exceed `255`.

For darker colors, decrease `100`. Don't go below `0`.

<br />
<br />

### Change Appearance / CSS
This snippet supports tweaking the look and feel of the cloud & tags using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how the tags in this snippet look:
- Open `Obsidian Settings`
- Install the `Style Settings` plugin
- Select `Style Settings` config panel under `Community Plugins`.
- Click the tab `Page Cloud - Version 1`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_4.gif"></p>

<br /><br />

**[`^        back to top        ^`](#table-of-contents-)**

<br /><br />