# Obsidian: Page Cloud : Version 1
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).

<br /><br />

## About
The `Page Cloud` snippet will fetch a list of all the pages that exist within your vault and list them in a cloud format. Certain pages will display different font sizes and colors depending on how populated the page is with contents.

<br />

This snippet supports `Frontmatter` values for the page names. For more information on this, view the customization section: [Page Titles](#page-titles).

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

---

<br /><br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/example_1.gif"></p>

<br /><br />

---

<br /><br />

## Usage

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Pick one of the two versions below, copy the code, and paste it in your Obsidian note.

<br />

Pick One:
1. [Normal Version](#normal-version)<br />Much easier to read the code
2. [Minified Version](#minified-version)<br />Much easier to paste

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
This version only formats the settings. All comments and formatting are removed.

<br />

````shell
```dataviewjs
const QueryStr          = `""`;
const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.5;
const minFontSize       = 12;
const maxFontSize       = 32;
const arrColors         = [];

const QueryStr='""',sortOption=1,weightBacklinks=.1,weightWordCount=.5,minFontSize=12,maxFontSize=32,arrColors=[],QueryFiles=dv.pages(QueryStr);for(let i=0;i<40;i++){let itemColor=`#${((Math.floor(100*Math.random()+100)<<16)+(Math.floor(100*Math.random()+100)<<8)+Math.floor(100*Math.random()+100)).toString(16)}`;arrColors.push(itemColor)}async function QueryBacklinks(e){let t=e.split("/").pop().split(".").slice(0,-1).join(".");return dv.query(`LIST FROM [[${t}]] AND ${QueryStr}`)}async function QueryWordcount(e){let t=require("fs"),r=require("path"),o=t.readFileSync(r.join(app.vault.adapter.basePath,e),"utf-8"),n=/---[\s\S]*?---|```[\s\S]*?```|\$[\s\S]*?\$|\$\$[\s\S]*?\$\$/g,a=o.replace(n,""),l=a.match(/\S+/g);return l?l.length:0}function Generate_FontSize(e,t){let r=2.5*Math.sqrt(e*weightBacklinks+t*weightWordCount);return Math.round(r/100*(maxFontSize-minFontSize)+minFontSize)}function Generate_Color(e){if(null==e)return"#FFFFFF";let t=e.split(/\W+/g),r=t.reduce((e,t)=>e+t.charCodeAt(0),0)%Object.keys(arrColors).length;return arrColors[Object.keys(arrColors)[r]]}function Sort_DESC(e){return e.sort((e,t)=>e.id.localeCompare(t.id)),e}function Sort_ASC(e){return e.sort((e,t)=>t.id.localeCompare(e.id)),e}function Sort_Shuffle(e){for(let t=e.length-1;t>0;t--){let r=Math.floor(Math.random()*(t+1));[e[t],e[r]]=[e[r],e[t]]}return e}const CreatePageCloud=async()=>{let files=new Map;Promise.all(QueryFiles.map(async e=>{let t=e.file,r=QueryBacklinks(t.path),o=QueryWordcount(t.path),n={backlinks:0,wordCount:0},a=await r;n.backlinks=a.value.values.length;let l=await o;n.wordCount=l,files.set(t,n)})).then(()=>{let data=[];files.forEach((e,t)=>{if(null==t)return;var r=t.frontmatter.name||t.frontmatter.title||t.frontmatter.alias,o=t.name;r&&(o=r);let n=Generate_FontSize(e.backlinks,e.wordCount),a=Generate_Color(t.name);data.push({name:o,id:t.name,size:t.size,path:t.path,fontSize:n,color:a})});let sortOptions={1:"Sort_DESC",2:"Sort_ASC",3:"Sort_Shuffle"},funcSort=sortOptions[sortOption];return void 0===funcSort&&(funcSort=sortOptions[1]),eval(funcSort)(data).map(e=>`<span class="page-cloud-v1-item"><a class="page-cloud-v1-link" href="obsidian://open?file=${encodeURIComponent(e.id)}" style="font-size:${e.fontSize}px; color: ${e.color};">${e.name}</a></span>`).join("")}).then(e=>dv.paragraph(e)).catch(e=>{console.error("Error: "+e)})};CreatePageCloud();
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
/*
    Snippet: Page Cloud - Version 1
*/

    /*
        animation: glow
    */

        @keyframes glow
        {
            from
            {
                text-shadow:            0 0 110px #6f00ff, 0 0 120px #0084ff, 0 0 130px #e60073, 0 0 140px #e60073, 0 0 150px #e60073, 0 0 160px #e60073, 0 0 170px #e60073;
            }
            to
            {
                text-shadow:            0 0 20px #6f00ff, 0 0 30px #ff4da6, 0 0 40px #ff4da6, 0 0 50px #ff4da6, 0 0 60px #ff4da6, 0 0 70px #ff4da6, 0 0 80px #ff4da6;
            }
        }

    /*
        animation: pulse
    */

        @keyframes pulse
        {
            0%
            {
                transform:              scale(0.85);
                box-shadow:             0 0 0 0 rgba(0, 0, 0, 0.7);
            }

            70%
            {
                transform:              scale(1);
                box-shadow:             0 0 0 10px rgba(0, 0, 0, 0);
            }

            100%
            {
                transform:              scale(0.85);
                box-shadow:             0 0 0 0 rgba(0, 0, 0, 0);
            }
        }

    /*
        Tag Cloud > Disable Colorful Animation
    */

        body.colorful-link-animation :is(.markdown-preview-view,.markdown-rendered) a:hover
        {
            animation:                  none !important; 
        }

    /*
        Tag Cloud > Item
    */

        .page-cloud-v1-item
        {
            margin-top:                 5px;
            margin-bottom:              5px;
            margin-left:                7px;
            margin-right:               7px;
            padding-left:               7px;
            padding-right:              7px;
            padding-top:                4px;
            padding-bottom:             4px;
            background-color:           #252525;
            border:                     1px solid #353535 !important;
            border-radius:              6px;
            display:                    inline-block;
            font-weight:                bold;
        }

        .page-cloud-v1-item:hover
        {
            opacity:                    1;
            background:                 #810d3d;
            border:                     1px solid #dd2a74 !important;
            cursor:                     pointer;
            animation-name:             pulse, anim_glow;
            animation-duration:         2s, 1s;
            animation-timing-function:  ease, ease-in-out;
            animation-iteration-count:  infinite, infinite;
            animation-direction:        normal, alternate;
        }

        .page-cloud-v1-item:hover a
        {
            color:                      #FFF !important;
            background:                 none;
        }

    /*
        Tag Cloud > Links
    */

        a.page-cloud-v1-link
        {
            line-height:                30px;
            vertical-align:             middle;
            text-decoration:            none;
        }
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `page_cloud_v1.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/install_3.gif"></p>

<br />

You should see a list of pages associated to your vault.

<br /><br />

---

<br /><br />

## Customization
The section below explains how to customize this snippet.

<br />
<br />

### Page Titles
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

### Change Sorting
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

### Cloud Colors
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