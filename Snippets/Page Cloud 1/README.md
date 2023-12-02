# Obsidian: Page Cloud : Version 1
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).

<br /><br />

## About
The `Page Cloud` snippet will fetch a list of all the pages that exist within your vault and list them in a cloud format. Certain pages will display different font sizes and colors depending on how populated the page is with contents.

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

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Page%20Cloud%201/images/example_1.gif"></p>

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
    Snippet: Page Cloud - Version 1

    This snippet requires the Dataview Plugin
      https://github.com/blacksmithgu/obsidian-dataview

    Creates a cloud which shows all of the pages that exist in your vault.

    sortOption
        1       = Sort Alphabetically (Descending)
        2       = Sort Alphabetically (Ascending)
        3       = Sort Random / Shuffle
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

    const tagWords      = tagName.split( /\W+/g );
    const colorIndex    = tagWords.reduce( ( total, word ) => total + word.charCodeAt( 0 ), 0 ) % Object.keys( colors ).length;

    return colors[ Object.keys( colors )[ colorIndex ] ];
}

/*
    Sort > DESC / ASC

    alphabetize array results
*/

function Sort_DESC( arr )
{
    arr.sort( ( a, b ) => a.name.localeCompare( b.name ) )
    return arr;
}

function Sort_ASC( arr )
{
    arr.sort( ( a, b ) => b.name.localeCompare( a.name ) )
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

            var file_name_fm   = fileName.frontmatter.name || fileName.frontmatter.title || fileName.frontmatter.alias;
            var file_name      = fileName.name

            if (file_name_fm)
                file_name      = file_name_fm;

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