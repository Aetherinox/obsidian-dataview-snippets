# Obsidian: Tag Cloud : Version 1
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).

<br /><br />

## About
The `Tag Cloud` snippet will fetch a list of your vault's current tags and display them in a colorful list.

<br />

At the time of writing this script, I am using the following:

| Software | Version |
| --- | --- |
| [Obsidian.md](https://obsidian.md/) | ![GitHub release](https://img.shields.io/github/v/release/obsidianmd/obsidian-releases?label=v&color=ba0f56) |
| [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview) | ![GitHub release](https://img.shields.io/github/v/release/blacksmithgu/obsidian-dataview?label=v&color=ba0f56) |

<br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%201/images/example_1.gif"></p>

<br /><br />

---

<br /><br />

## Usage

- Install [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview)
- Copy the code below and paste it in a note.

<br />

````shell
```dataviewjs
/*
    Snippet: Tag Cloud - Version 1

    This snippet requires the Dataview Plugin
        https://github.com/blacksmithgu/obsidian-dataview

    View settings at:
        https://github.com/Aetherinox/obsidian-dataview-snippets/tree/main/Snippets/Tag%20Cloud%201
*/

/*
    Settings
*/

const QueryStr          = `""`;
const QueryFiles        = dv.pages( QueryStr );

const bRandomColor      = true;
const sortOption        = 1;
const weightBacklinks   = 0.1;
const weightWordCount   = 0.3;
const minFontSize       = 12;
const maxFontSize       = 32;
const tagsFilter        = [ "#tag1", "#tag2" ];
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
        return `<div class="cloudtags-v1-item"><a class="cloudtags-v1-link" href="obsidian://search?query=tag:${encodeURIComponent(tag.id)}" style="font-size:${tag.fontSize}px; color: ${tag.color};">${tag.id}</a><div class="tagcloud-v1-length">${tag.length}</div></div>`;
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

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%201/images/install_1.gif"></p>

<br />

Create a new file named whatever (`tag_cloud_v1.css` in our example).

Copy the code below and paste it into the new `tag_cloud_v1.css` file which should be in `YourVaultName/.obsidian/snippets/tag_cloud_v1.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%201/images/install_2.png"></p>

<br />

```css
/*
    Snippet: Tag Cloud - Version 1
*/

    /*
        animation: glow
    */

        @keyframes anim_glow
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
        Tag Cloud > Item
    */

        .cloudtags-v1-item
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
            position:                   relative;
        }

        .cloudtags-v1-item:hover
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

        .cloudtags-v1-item:hover a
        {
            color:                      #FFF !important;
            background:                 none;
        }

    /*
        Tag Cloud > Links
    */

        a.cloudtags-v1-link
        {
            line-height:                30px;
            vertical-align:             middle;
            text-decoration:            none;
        }

    /*
        Tag Cloud > Length
    */

        .tagcloud-v1-length
        {
            border-radius:              50%;
            width:                      18px;
            height:                     18px;
            background:                 #424242;
            color:                      #FFF;
            text-align:                 center;
            font:                       8px sans-serif;
            position:                   absolute;
            vertical-align:             middle;
            margin:                     auto 0;
            left:                       3%;
            top:                        -5px;
            transform:                  translateX(-50%);
            line-height:                19px;
          }
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `tag_cloud_v1.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud%201/images/install_3.gif"></p>

<br />

You should see a list of tags associated to your vault.

<br /><br />

---

<br /><br />

## Customization
The section below explains how to customize this snippet.

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