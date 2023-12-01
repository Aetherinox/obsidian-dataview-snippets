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
*/

/*
    Settings
*/

const QueryStr          = `""`;
const QueryFiles        = dv.pages( QueryStr );

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
    Get all md files and associated tags
*/

async function tagsQuery( q )
{
    return dv.query(
    `
        LIST
        FROM "${q}"
        where tags != ""
        SORT file.name DESC
    `);
}

async function tagFilesQuery( q )
{
    return dv.query(
    `
        LIST
        FROM ${QueryStr}
        where tags = "${q}"
        SORT file.name DESC
    `);
}

/*
    Get number of words in each file
*/

async function QueryWordcount( q )
{
    const fs            = require( 'fs' );
    const path          = require( 'path' );
    const text          = fs.readFileSync( path.join( app.vault.adapter.basePath, q ), 'utf-8' );

    /*
        Remove blocks
    */

    const pattern       = /---[\s\S]*?---|```[\s\S]*?```|\$[\s\S]*?\$|\$\$[\s\S]*?\$\$/g;
    const cleanedText   = text.replace( pattern, '' );

    /*
        Count words

        return cleanedText.match(/\S+/g).length;
    */

    return 20;
}

/*
    Calculate font text size which is determined by number of backlinks
    and number of words available.
*/

function Generate_FontSize( backlinks, wordCount )
{
    const weightBacklinks       = 0.1;
    const weightWordCount       = 0.5;
    const minFontSize           = 14;
    const maxFontSize           = 28;
    const calcFontSize          = Math.sqrt( ( backlinks * weightBacklinks ) + ( wordCount * weightWordCount ) ) * 2.5;

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

        "grey-2": "#5e5e5e",
        "grey-3": "#717171",
        "teal-2": "#52b788",
        "teal-3": "#c4fff9",
        "cyan-2": "#4dd0e1",
        "cyan-3": "#26c6da",

        "blue-1": "#81d4fa",
        "blue-2": "#4fc3f7",
        "blue-3": "#29b6f6",
        "blue-4": "#03a9f4",

        "indigo-1": "#7986cb",
        "indigo-2": "#3f51b5",
        "indigo-3": "#ba68c8",
        "indigo-4": "#ab47bc",
        "indigo-5": "#d500f9",

        "red-1": "#f06292",
        "red-2": "#e91e63",
        "red-3": "#f50057",

    
        "orange": "#ffb364",
        "yellow": "#ffd664",
        "green": "#64ff64",
        "blue": "#64bfff",
        "purple": "#b864ff",
        "ruby-red": "#D62E4D",
        "sunny-yellow": "#F6C026",
        "vivid-orange": "#FF6E1F",
        "bright-pink": "#F1478A",
        "electric-blue": "#0F7DC2",
        "deep-purple": "#5B0F91",
        "teal-green": "#007F86",
        "golden-brown": "#AA8F6A",
        "moss-green": "#8BC34A",
        "navy-blue": "#3F51B5",
        "pale-pink": "#F7B1B1",
        "soft-lilac": "#C9A1E9",
        "pastel-green": "#B3E6C3",
        "sky-blue": "#87CEEB",
        "light-gray": "#D3D3D3",
        "chocolate-brown": "#5F4B32",
        "cream-yellow": "#FFFDD0",
        "peach-orange": "#FFCC99",
        "dusty-rose": "#C4A4A4",
        "seafoam-green": "#71BC9C"
    };

    const tagWords      = tagName.split(/\W+/g);
    const colorIndex    = Math.floor( Math.random( ) * 30 );
    const colorID       = dv.pages( tagName ).length;

    return colors[ Object.keys( colors )[ colorID ] ];
}

/*
    Sort > Desc

    alphabetize array results
*/

function Sort_DESC( arr )
{
    arr.sort( ( a, b ) => a.id.localeCompare( b.id ) )
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
            }));
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
    
        data.push( { name: `\\${tagName}`, id: tagName, fontSize, color } );
    });

    /*
        Return results
    */

    return Sort_DESC( data ).map( ( tag ) =>
    {
        return `<span class="cloudtags-item"><a class="cloudtags-link" href="obsidian://search?query=tag:${encodeURIComponent(tag.id)}" style="font-size:${tag.fontSize}px; color: ${tag.color};">${tag.name}</a></span>`;
    }).join("");
    }).then( res => dv.paragraph( res ) )
    .catch( error =>
    {
        console.error( error );
    });
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
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `toc.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Tag%20Cloud/images/install_3.gif"></p>

<br />

You should now see either a list of broken links that exist within your vault, or a happy face and an "All Clear" message.