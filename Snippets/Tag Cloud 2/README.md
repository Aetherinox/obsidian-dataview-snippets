# Obsidian: Tag Cloud - Version 2 <!-- omit from toc -->
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
  - [Filtering Folders, Pages \& Tags](#filtering-folders-pages--tags)
    - [Exclude Folders](#exclude-folders)
    - [Exclude Page](#exclude-page)
    - [Exclude Tags](#exclude-tags)
  - [Change Sorting](#change-sorting)
  - [Random Tag Colors](#random-tag-colors)
  - [Font Size](#font-size)
  - [Font Weight](#font-weight)
  - [Cloud Colors](#cloud-colors)
  - [Change Appearance / CSS](#change-appearance--css)

<br />

---

<br /><br />

## About
The `Tag Cloud: Version 2` snippet will fetch a list of your vault's current tags and display them in a colorful list.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |

<br /><br />

> [!WARNING] WARNING - Using Special Characters In Tags
> Obsidian currently doesn't support using periods within tags. If you have any tags that contain a period, your tag list may not render.
> 
> ex: `#my.tag`

<br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />

---

<br /><br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/example_1.gif"></p>

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
/*
    Snippet: Tag Cloud - Version 2

    This snippet requires the Dataview Plugin
        https://github.com/blacksmithgu/obsidian-dataview

    View settings at:
        https://github.com/Aetherinox/obsidian-dataview-snippets/tree/main/Snippets/Tag%20Cloud%202
*/

/*
    Settings
*/

const QueryStr          = `""`;
const QueryFiles        = dv.pages( QueryStr );

const bRandomColor      = true;
const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.5;
const minFontSize       = 12;
const maxFontSize       = 32;
const tagsFilter        = [ "#tag1", "#tag2" ];
const arrColors         = [];

dv.container.className += ' atx-tcv2-dataview'

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
    SORT file.name DESC
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

function Generate_Color( tagName, tagInfo )
{
    if ( tagName == null ) { return "#FFFFFF"; }

    let cntColors       = Object.keys( arrColors ).length;
    const tagWords      = tagName.split(/\W+/g);
    const colorIndex    = Math.floor( Math.random( ) * cntColors );
    const colorID       = dv.pages( tagName ).length;

    if ( bRandomColor === true )
        return arrColors[ Object.keys( arrColors )[ colorIndex ] ];

    return arrColors[ Object.keys( arrColors )[ colorID ] ];
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
    Create Cloud
*/

const CreateTagCloud = async ( ) =>
{
    const tags      = new Map( );
    const files     = new Map( );

    /*
        Add all .md files to the tags map with their backlinks and word count
    */

    Promise.all( QueryFiles.map( async ( f ) =>
    {
        const file  = f.file
        const blq   = QueryBacklinks( file.path )
        const wcq   = QueryWordcount( file.path )

        if ( file.tags )
        {
            await Promise.all( file.tags.map( async ( tag ) =>
            {
                if ( !tags.has( tag ) )
                    tags.set( tag, { backlinks: 0, wordCount: 0 } );

                const tagInfo       = tags.get( tag );
                const res           = await blq;
        
                tagInfo.backlinks += res.value.values.length;
        
                const wc            = await wcq;
                tagInfo.wordCount += wc;
            } ) );
        }

        for ( let i = 0; i < tagsFilter.length; i++ )
        {
            if ( tags.has( tagsFilter[ i ] ) )
                tags.delete( tagsFilter[ i ] );
        }

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

    tags.forEach( ( tagInfo, tagName ) =>
    {
        const fontSize      = Generate_FontSize( tagInfo.backlinks, tagInfo.wordCount );
        const color         = Generate_Color( tagName, tagInfo );
        const length        = dv.pages( tagName ).length;
		
        data.push( { name: `\\${tagName}`, id: tagName, length: length, fontSize, color } );
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
        return `<div class="atx-tcv2-child atx-tcv2-item-tags"><a class="atx-tcv2-link" href="obsidian://search?query=tag:${encodeURIComponent(tag.id)}" style="font-size:${tag.fontSize}px; color: ${tag.color};">${tag.id}</a><div class="atx-tcv2-counter">${tag.length}</div></div>`;
    } ).join( "" );
    } ).then( res => dv.paragraph( res ) )
    .catch( error =>
    {
        console.error( "Error: " + error );
    } );
}

CreateTagCloud( )
```
````

<br />

#### Minified Version
This version only formats the settings. All other formatting and comments are removed.

<br />

````shell
```dataviewjs
const QueryStr          = `""`;
const QueryFiles        = dv.pages( QueryStr );

const bRandomColor      = true;
const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.5;
const minFontSize       = 12;
const maxFontSize       = 32;
const tagsFilter        = [ "#tag1", "#tag2" ];

const arrColors=[];dv.container.className+=" atx-tcv2-dataview";for(let i=0;i<40;i++){let itemColor=`#${((Math.floor(100*Math.random()+100)<<16)+(Math.floor(100*Math.random()+100)<<8)+Math.floor(100*Math.random()+100)).toString(16)}`;arrColors.push(itemColor)}async function QueryBacklinks(t){let e=t.split("/").pop().split(".").slice(0,-1).join(".");return dv.query(`LIST FROM [[${e}]] AND ${QueryStr} SORT file.name DESC`)}async function QueryWordcount(t){let e=require("fs"),r=require("path"),a=e.readFileSync(r.join(app.vault.adapter.basePath,t),"utf-8"),o=/---[\s\S]*?---|```[\s\S]*?```|\$[\s\S]*?\$|\$\$[\s\S]*?\$\$/g,l=a.replace(o,""),n=l.match(/\S+/g);return n?n.length:0}function Generate_FontSize(t,e){let r=2.5*Math.sqrt(t*weightBacklinks+e*weightWordCount);return Math.round(r/100*(maxFontSize-minFontSize)+minFontSize)}function Generate_Color(t,e){if(null==t)return"#FFFFFF";let r=Object.keys(arrColors).length;t.split(/\W+/g);let a=Math.floor(Math.random()*r),o=dv.pages(t).length;return!0===bRandomColor?arrColors[Object.keys(arrColors)[a]]:arrColors[Object.keys(arrColors)[o]]}function Sort_DESC(t){return t.sort((t,e)=>t.id.localeCompare(e.id)),t}function Sort_ASC(t){return t.sort((t,e)=>e.id.localeCompare(t.id)),t}function Sort_Shuffle(t){for(let e=t.length-1;e>0;e--){let r=Math.floor(Math.random()*(e+1));[t[e],t[r]]=[t[r],t[e]]}return t}const CreateTagCloud=async()=>{let tags=new Map,files=new Map;Promise.all(QueryFiles.map(async t=>{let e=t.file,r=QueryBacklinks(e.path),a=QueryWordcount(e.path);e.tags&&await Promise.all(e.tags.map(async t=>{tags.has(t)||tags.set(t,{backlinks:0,wordCount:0});let e=tags.get(t),o=await r;e.backlinks+=o.value.values.length;let l=await a;e.wordCount+=l}));for(let o=0;o<tagsFilter.length;o++)tags.has(tagsFilter[o])&&tags.delete(tagsFilter[o]);let l={backlinks:0,wordCount:0},n=await r;l.backlinks=n.value.values.length;let s=await a;l.wordCount=s,files.set(e,l)})).then(()=>{let data=[];tags.forEach((t,e)=>{let r=Generate_FontSize(t.backlinks,t.wordCount),a=Generate_Color(e,t),o=dv.pages(e).length;data.push({name:`\\${e}`,id:e,length:o,fontSize:r,color:a})});let sortOptions={1:"Sort_DESC",2:"Sort_ASC",3:"Sort_Shuffle"},funcSort=sortOptions[sortOption];return void 0===funcSort&&(funcSort=sortOptions[1]),eval(funcSort)(data).map(t=>`<div class="atx-tcv2-child atx-tcv2-item-tags"><a class="atx-tcv2-link" href="obsidian://search?query=tag:${encodeURIComponent(t.id)}" style="font-size:${t.fontSize}px; color: ${t.color};">${t.id}</a><div class="atx-tcv2-counter">${t.length}</div></div>`).join("")}).then(t=>dv.paragraph(t)).catch(t=>{console.error("Error: "+t)})};CreateTagCloud();
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

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/install_1.gif"></p>

<br />

Create a new file named whatever (`tag_cloud_v2.css` in our example).

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/install_2.png"></p>

<br />

Copy the code below and paste it into the new `tag_cloud_v2.css` file which should be in `YourVaultName/.obsidian/snippets/tag_cloud_v2.css`

<br />

```css
/* @settings
name: ☁️ Tag Clouds - Version 2
id: atx-tcv2
settings:
-
    id:                     atx-tcv2-cat-general
    title:                  '1. Appearance'
    description:            'General appearance settings'
    type:                   heading
    level:                  1
    collapsed:              true


-
        id:                 atx-tcv2-general-about
        title:              ''
        description:        "<br /><br /><h1>General</h1>General settings not categorized anywhere else."
        type:               info-text
        markdown:           true
-
        id:                 atx-tcv2-general-container-padding
        title:              'Container Padding'
        description:        'Amount of padding between edge of tags and note width'
        type:               variable-number-slider
        default:            20
        format:             px
        min:                0
        max:                50
        step:               1
-
        id:                 atx-tcv2-general-line-spacing
        title:              'Height between tag on each row'
        description:        'Font size for header description text.'
        type:               variable-number-slider
        default:            15
        format:             px
        min:                0
        max:                30
        step:               1


-


        id:                 atx-tcv2-tags-about
        title:              ''
        description:        "<br /><br /><h1>Tags</h1>Settings for customizing tags."
        type:               info-text
        markdown:           true
-
        id:                 atx-tcv2-tags-font-face
        title:              'Font Face'
        description:        'Font for tags'
        type:               variable-text
        default:            'sans-serif'
-
        id:                 atx-tcv2-tags-font-clr-h
        title:              'Font Color (Hover)'
        description:        'Color of tag text when tag is hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'
-
        id:                 atx-tcv2-tags-font-weight-n
        title:              'Font Weight (Normal)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-tcv2-tags-font-weight-h
        title:              'Font Weight (Hover)'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal
-
        id:                 atx-tcv2-tags-spacing-internal
        title:              'Spacing (Internal)'
        description:        'Spacing between left and right of tag text and edge of each container'
        type:               variable-number-slider
        default:            5
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-tcv2-tags-spacing-external
        title:              'Spacing (External)'
        description:        'Spacing between each container box'
        type:               variable-number-slider
        default:            7
        format:             px
        min:                0
        max:                30
        step:               1


-


        id:                 atx-tcv2-counter-about
        title:              ''
        description:        "<br /><br /><h1>Counter</h1>Settings for tag indicator count at top left of each tag when hovered."
        type:               info-text
        markdown:           true
-
        id:                 atx-tcv2-counter-bg-clr
        title:              'Background Color'
        description:        'Background color for circle'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#424242'
        default-dark:       '#424242'
-
        id:                 atx-tcv2-counter-bg-size
        title:              'Circle Size'
        description:        'Size of counter circle'
        type:               variable-number-slider
        default:            18
        format:             px
        min:                5
        max:                30
        step:               1
-
        id:                 atx-tcv2-counter-font-face
        title:              'Font Face'
        description:        'Fonts except from the code texts'
        type:               variable-text
        default:            'sans-serif'
-
        id:                 atx-tcv2-counter-font-clr-n
        title:              'Font Color'
        description:        'Color of tag text when tag is hovered.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'
-
        id:                 atx-tcv2-counter-font-size
        title:              'Font Size'
        description:        'Font size for counter'
        type:               variable-number-slider
        default:            10
        format:             px
        min:                5
        max:                30
        step:               1
-
        id:                 atx-tcv2-counter-font-weight
        title:              'Font Weight'
        description:        'Options: normal, bold, lighter, bolder'
        type:               variable-text
        default:            normal


-


    id:                     atx-tcv2-cat-anim
    title:                  '2. Animations'
    description:            'Animation settings'
    type:                   heading
    level:                  1
    collapsed:              true
-
        id:                 atx-tcv2-cat-anim-1
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
        id:                 atx-tcv2-cat-anim-2
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
        id:                 atx-tcv2-cat-anim-3
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


    id:                     atx-tcv2-cat-support
    title:                  '3. Support'
    description:            'Links associated to this snippet'
    type:                   heading
    level:                  1
    collapsed:              true
-
    id:                     atx-tcv2-support-updates
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
            transition: none;
        }

    /*
        Snippet: Tag Cloud - Version 2
    */

        body
        {
            --atx-tcv2-anim-1-glow:                     atx-tcv2-anim-glow;
            --atx-tcv2-anim-2-in:                       atx-tcv2-anim-scale-in;
            --atx-tcv2-anim-2-out:                      atx-tcv2-anim-scale-out;
            --atx-tcv2-anim-2-scale:                    scale( 1.3 );
            --atx-tcv2-anim-3-trans:                    opacity .3s ease-in-out;
            --atx-tcv2-anim-3-opacity:                  0.5;

            --atx-tcv2-general-line-spacing:            15px;
            --atx-tcv2-general-container-padding:       20px;

            --atx-tcv2-tags-font-face:                  sans-serif;
            --atx-tcv2-tags-font-weight-n:              normal;
            --atx-tcv2-tags-font-weight-h:              normal;
            --atx-tcv2-tags-spacing-internal:           5px;
            --atx-tcv2-tags-spacing-external:           7px;

            --atx-tcv2-counter-bg-clr:                  #424242;
            --atx-tcv2-counter-bg-size:                 18px;
            --atx-tcv2-counter-font-face:               sans-serif;
            --atx-tcv2-counter-font-clr-n:              #FFFFFF;
            --atx-tcv2-counter-font-size:               10px;
            --atx-tcv2-counter-font-weight:             normal;
        }

    /*
        Settings > Animations
    */

        body.theme-light.anim-1-disabled,
        body.theme-dark.anim-1-disabled
        {
            --atx-tcv2-anim-1-glow:     none !important;
        }
        
        body.theme-light.anim-1-enabled,
        body.theme-dark.anim-1-enabled
        {
            --atx-tcv2-anim-1-glow:     atx-tcv2-anim-glow;
        }

        body.theme-light.anim-2-disabled,
        body.theme-dark.anim-2-disabled
        {
            --atx-tcv2-anim-2-in:       none;
            --atx-tcv2-anim-2-out:      none;
            --atx-tcv2-anim-2-scale:    scale( 1 );
        }
        
        body.theme-light.anim-2-enabled,
        body.theme-dark.anim-2-enabled
        {
            --atx-tcv2-anim-2-in:       atx-tcv2-anim-scale-in;
            --atx-tcv2-anim-2-out:      atx-tcv2-anim-scale-out;
            --atx-tcv2-anim-2-scale:    scale( 1.3 );
        }

        body.theme-light.anim-3-disabled,
        body.theme-dark.anim-3-disabled
        {
            --atx-tcv2-anim-3-trans:    none;
            --atx-tcv2-anim-3-opacity:  1;
        }
        
        body.theme-light.anim-3-enabled,
        body.theme-dark.anim-3-enabled
        {
            --atx-tcv2-anim-3-trans:    opacity .3s ease-in-out;
            --atx-tcv2-anim-3-opacity:  0.5;
        }

    /*
        animation: glow
    */

        @keyframes atx-tcv2-anim-glow
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
        animation: scale
    */

        @keyframes atx-tcv2-anim-scale-in
        {
            0%
            {
                transform:          scale( 1 );
            }
            100%
            {
                transform:          scale( 1.3 );
            }
        }
          
        @keyframes atx-tcv2-anim-scale-out
        {
            0%
            {
                transform:          scale( 1.3 );
            }
            100%
            {
                transform:          scale( 1 );
            }
        }

    /*
        Dataview parent div
    */

        .atx-tcv2-dataview
        {
            padding-left:           var( --atx-tcv2-general-container-padding );
            padding-right:          var( --atx-tcv2-general-container-padding );
        }

    /*
        Hover effect

        All children are dimmed when a single tag is hovered
    */

        .atx-tcv2-dataview:hover .atx-tcv2-child
        {
            transition:             var( --atx-tcv2-anim-3-trans );
            opacity:                var( --atx-tcv2-anim-3-opacity );
        }

        .atx-tcv2-dataview .atx-tcv2-child:hover
        {
            transition:             var( --atx-tcv2-anim-3-trans );
            opacity:                1;
        }

        .atx-tcv2-dataview .atx-tcv2-child
        {
            transition:             var( --atx-tcv2-anim-3-trans );
            opacity:                1;
        }

    /*
        Tag Cloud > Item
    */

        .atx-tcv2-item-tags
        {
            margin:                     5px var( --atx-tcv2-tags-spacing-external ) 5px var( --atx-tcv2-tags-spacing-external );
            padding:                    4px var( --atx-tcv2-tags-spacing-internal ) 4px var( --atx-tcv2-tags-spacing-internal );
            display:                    inline-block;
            position:                   relative;
            animation-name:             var( --atx-tcv2-anim-2-out );
            animation-duration:         0.3s;
            animation-timing-function:  ease;
            animation-iteration-count:  1;
            animation-direction:        normal;
        }

        .atx-tcv2-item-tags:hover
        {
            transform:                  var( --atx-tcv2-anim-2-scale );
            cursor:                     pointer;
            animation-name:             var( --atx-tcv2-anim-1-glow ), var( --atx-tcv2-anim-2-in );
            animation-duration:         1s, 0.3s;
            animation-timing-function:  ease-in-out, ease-in-out;
            animation-iteration-count:  infinite, 1;
            animation-direction:        alternate, normal;
            background:                 none;
        }

        .atx-tcv2-item-tags:hover a
        {
            color:                      var( --atx-tcv2-tags-font-clr-h ) !important;
            background:                 none;
            font-weight:                var( --atx-tcv2-tags-font-weight-h ) !important;
        }

    /*
        Tag Cloud > Links
    */

        a.atx-tcv2-link
        {
            line-height:                var( --atx-tcv2-general-line-spacing );
            vertical-align:             middle;
            text-decoration:            none;
            font-weight:                var( --atx-tcv2-tags-font-weight-n ) !important;
            font-family:                var( --atx-tcv2-tags-font-face ) !important;
        }

    /*
        Tag Cloud > Display Length Div on Item Hover
    */

        .atx-tcv2-item-tags:hover .atx-tcv2-counter
        {
            font-weight:                bold !important;
            display:                    block;
        }

    /*
        Tag Cloud > Length
    */

        .atx-tcv2-counter
        {
            font-weight:                var( --atx-tcv2-counter-font-weight );
            display:                    none;
            border-radius:              50%;
            width:                      var( --atx-tcv2-counter-bg-size );
            height:                     var( --atx-tcv2-counter-bg-size );
            background:                 var( --atx-tcv2-counter-bg-clr );
            color:                      var( --atx-tcv2-counter-font-clr-n );
            text-align:                 center;
            font-size:                  var( --atx-tcv2-counter-font-size );
            font-family:                var( --atx-tcv2-counter-font-face );
            position:                   absolute;
            vertical-align:             middle;
            margin:                     auto 0;
            left:                       3%;
            top:                        -5px;
            transform:                  translateX( -50% );
            line-height:                var( --atx-tcv2-counter-bg-size );
          }
```

<br />
<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `tag_cloud_v2.css`.

<br />
<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/install_3.gif"></p>

<br />

You should see a list of pages associated to your vault.

<br />

This snippet supports modifying the CSS values using the **[Style Settings](https://github.com/mgmeyers/obsidian-style-settings)** plugin. If you want to change how the tags in this snippet look:
- Open `Obsidian Settings`
- Install the `Style Settings` plugin
- Select `Style Settings` config panel under `Community Plugins`.
- Click the tab `Tag Cloud - Version 2`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/install_4.gif"></p>

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

### Filtering Folders, Pages & Tags
This snippet allows you to filter out folders, pages and tags which will not be included in your list of generated tags that appear in your cloud. The syntax is the same as normal Dataview queries.

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

#### Exclude Tags
To filter out specific tags, this is done slightly different than the other options above. You can prevent certain tags from appearing in your cloud by modifying the following property:

<br />

```javascript
const tagsFilter = [ "#tag1", "#tag2" ];
```

<br />

Ensure you use the structure provided. Each tag must be wrapped in quotation marks `"`, with a comma `,` separating each one.

<br />
<br />

### Change Sorting
This snippet allows for two ways of sorting tags:
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

### Random Tag Colors
Tags can either be assigned random colors, or colors can be assigned depending on how many times that tag has been called.

<br />

To change this, edit the property:

```javascript
const bRandomColor = true;
```

<br />
<br />

### Font Size
The font size of a tag is determined by two things:
1. Number of times a tag is used
2. Number of words associated to a tag.

<br />

To limit the font sizes that are used, edit the two properties below:

```javascript
const minFontSize = 12;
const maxFontSize = 32;
```

<br />
<br />

### Font Weight
The font weight of a tag is determined by two things:
1. Number of times a tag is used
2. Number of words associated to a tag.

<br />

To limit the font weight sizes used, edit the two properties below. The lower the value, the thinner the tag. Higher numbers will display more bold text. This setting also plays a role in the [Font Size](#font-size)

```javascript
const weightBacklinks = 0.1;
const weightWordCount = 0.3;
```

<br />
<br />

### Cloud Colors
As of `v1.3.0`, this snippet auto generates the colors that will be used for each tag listed in the cloud. The color generation code will keep the color tones in the **pastel** range, and will exclude darker colors since the background box color is dark.

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
- Click the tab `Tag Cloud - Version 2`
- Edit the settings for the Page Cloud tags

<br />

<p align="center"><img style="width: 70%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%202/images/install_4.gif"></p>

<br /><br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents-)**

</div>

<br /><br />