# Obsidian: Tag Cloud
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

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud/images/example_1.gif"></p>

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
    Snippet: Tag Cloud

    This snippet requires the Dataview Plugin
      https://github.com/blacksmithgu/obsidian-dataview

    Creates a cloud with all of your tags which are clickable to
    search for associated pages related to the selected cloud.

    sortOption
        1       = Sort Alphabetically
        2       = Sort Random / Shuffle

    bRandomColor
        true    = Uses random colors for each tag
        false   = Tags with more uses gets more colorful
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
    const colors =
    {
        "grey-01": "#636363",
        "grey-02": "#777777",
        "grey-03": "#8e8e8e",
        "grey-1": "#c8c9cb",
        "teal-1": "#cfebf1",
        "green-1": "#b3dfce",
        "yellow-1": "#f4e3bd",
        "orange-1": "#f4d4b3",
        "red-1": "#ddb6b9",
        "fuchsia-1": "#fbb7ce",
        "purple-1": "#d0c2df",
        "blue-1": "#c5cadf",
        "dblue-1": "#c5c6cc",
        "grey-2": "#a3a5a8",
        "teal-2": "#b0dde8",
        "green-2": "#80c9ae",
        "yellow-2": "#edd091",
        "orange-2": "#edb780",
        "red-2": "#c7868a",
        "fuchsia-2": "#f987ae",
        "purple-2": "#b09ac9",
        "blue-2": "#9ea6c9",
        "dblue-2": "#9ea1ab",
        "grey-3": "#7e8085",
        "teal-3": "#90cfde",
        "green-3": "#4db38e",
        "yellow-3": "#e6bd64",
        "orange-3": "#e59a4d",
        "red-3": "#b1565b",
        "fuchsia-3": "#f6578d",
        "purple-3": "#9071b3",
        "blue-3": "#7782b3",
        "dblue-3": "#777b89",
        "teal-4": "#60bbd0",
        "green-4": "#00935d",
        "yellow-4": "#dba022",
        "orange-4": "#da6f01",
        "red-4": "#a93c43",
        "fuchsia-4": "#f20f5c",
        "purple-4": "#613493",
        "blue-4": "#3c4d93",
        "grey-5": "#535f77",
        "green-5": "#008454",
        "yellow-5": "#c5901f",
        "orange-5": "#c57a2d",
        "red-5": "#b93f45",
        "red-6": "#9b3a40",
        "fuchsia-5": "#da0e53",
        "fuchsia-6": "#bf2458",
    };

    let cntColors       = Object.keys( colors ).length;
    const tagWords      = tagName.split(/\W+/g);
    const colorIndex    = Math.floor( Math.random( ) * cntColors );
    const colorID       = dv.pages( tagName ).length;

    if ( bRandomColor === true )
        return colors[ Object.keys( colors )[ colorIndex ] ];

    return colors[ Object.keys( colors )[ colorID ] ];
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
    Create cloud tags
*/

const CreateTags = async ( ) =>
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
        return `<div class="cloudtags-item"><a class="cloudtags-link" href="obsidian://search?query=tag:${encodeURIComponent(tag.id)}" style="font-size:${tag.fontSize}px; color: ${tag.color};">${tag.id}</a><div class="tagcloud-length">${tag.length}</div></div>`;
    } ).join( "" );
    } ).then( res => dv.paragraph( res ) )
    .catch( error =>
    {
        console.error( "Error: " + error );
    } );
}

CreateTags( )
```
````

<br />

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud/images/install_1.gif"></p>

<br />

Create a new file named whatever (`tag_cloud.css` in our example).

Copy the code below and paste it into the new `tag_cloud.css` file which should be in `YourVaultName/.obsidian/snippets/tag_cloud.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud/images/install_2.png"></p>

<br />

```css
/*
    Snippet: Tag Cloud
*/

    /*
        animation: glow
    */

        @keyframes glow
        {
            from
            {
                text-shadow:      0 0 110px #6f00ff, 0 0 120px #0084ff, 0 0 130px #e60073, 0 0 140px #e60073, 0 0 150px #e60073, 0 0 160px #e60073, 0 0 170px #e60073;
            }
            to
            {
                text-shadow:      0 0 20px #6f00ff, 0 0 30px #ff4da6, 0 0 40px #ff4da6, 0 0 50px #ff4da6, 0 0 60px #ff4da6, 0 0 70px #ff4da6, 0 0 80px #ff4da6;
            }
        }

    /*
        animation: pulse
    */

        @keyframes pulse
        {
            0%
            {
                transform: scale(0.85);
                box-shadow: 0 0 0 0 rgba(0, 0, 0, 0.7);
            }

            70%
            {
                transform: scale(1);
                box-shadow: 0 0 0 10px rgba(0, 0, 0, 0);
            }

            100%
            {
                transform: scale(0.85);
                box-shadow: 0 0 0 0 rgba(0, 0, 0, 0);
            }
        }

    /*
        Tag Cloud > Item
    */

        .cloudtags-item
        {
            margin-top: 5px;
            margin-bottom: 5px;
            margin-left: 7px;
            margin-right: 7px;
            padding-left: 7px;
            padding-right: 7px;
            padding-top: 4px;
            padding-bottom: 4px;
            background-color: #252525;
            border: 1px solid #353535 !important;
            border-radius: 6px;
            display: inline-block;
            font-weight: bold;
            position:relative;
        }

        .cloudtags-item:hover
        {
            opacity: 0.9;
            background: #810d3d;
            animation: pulse 2s infinite !important;
            border: 1px solid #dd2a74 !important;
            cursor: pointer;
        }

        .cloudtags-item:hover a
        {
            color:              #FFF !important;
            background:         none;
            -webkit-animation:  glow 1s ease-in-out infinite alternate !important;
            -moz-animation:     glow 1s ease-in-out infinite alternate  !important;
            animation:          glow 1s ease-in-out infinite alternate  !important;
        }

    /*
        Tag Cloud > Links
    */

        a.cloudtags-link
        {
            line-height: 30px;
            vertical-align: middle;
            text-decoration: none;
        }

    /*
        Tag Cloud > Length
    */

        .tagcloud-length
        {
            border-radius: 50%;
            width: 18px;
            height: 18px;
            background: #424242;
            color: #FFF;
            text-align: center;
            font: 8px sans-serif;
            position: absolute;
            vertical-align: middle;
            margin: auto 0;
            left: 3%;
            top: -5px;
            transform: translateX(-50%);
            line-height: 19px;
          }
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `toc.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud/images/install_3.gif"></p>

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

To filter out folders, pages or tags, edit the following:

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
To filter out specific tags, this is done slightly differently than the other options above. You can prevent certain tags from appearing in your cloud by modifying the following property:

<br />

```javascript
const tagsFilter = [ "#tag1", "#tag2" ];
```

<br />

Ensure you use the structure provided. Each tag must be wrapped in quotation marks `"`, with a comma `,` separating each one.

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