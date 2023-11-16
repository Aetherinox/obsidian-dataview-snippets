# Obsidian: Bad Links Snippet
This snippet requires a copy of [Obsidian.md](obsidian.md/)
<br />
This snippet requires the [Dataview Plugin](https://github.com/blacksmithgu/obsidian-dataview).

<br /><br />

## About
The `Table of Contents: Bad Links` snippet displays a list of internal links within your vault that lead nowhere.

To fix these, you can either delete the link on the associated page, or you can click each item in the list and create a new page.

Once the link has been fixed, it will be removed from the list.

<br />

### Previews
The following are preview images of what the snippet will do and appear like:

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/example_1.png"></p>

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/example_2.gif"></p>

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/example_3.png"></p>

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
    Table of Contents Script > Bad Links

    This snippet requires the Dataview Plugin
      https://github.com/blacksmithgu/obsidian-dataview

    Create a new note and paste the code below.
*/

const minRequired       = 0;
let count               = 0;

//const link_filter     = ( a ) => a.toUpperCase()
const link_filter       = ( a ) => a.toUpperCase().replace(/[\s/\\]+/,"")

const arr = Object.entries( dv.app.metadataCache.unresolvedLinks ) 
      .filter( ( [ k, v ] )=> Object.keys( v ).length ) 
      .flatMap( ( [ k, v ] ) => 
      Object.keys( v ).map( x => 
            ({
                  key: link_filter( x ),
                  title: dv.fileLink( k ),
                  title_path: k,
                  link_orig: `${ dv.fileLink( x ) }`,
                  link_src: `${ dv.fileLink( k ) }`,
                  link_src_orig: `${ dv.fileLink( k ) } ( ${ dv.fileLink( x ) } )`,
                  list: `${ dv.fileLink( x ) }`
            })
      ))
      .sort( ( a, b ) => dv.compare( a.key, b.key ) )

      dv.list(
            dv.array( arr )
                  .groupBy( t => t.link_src )
                  .where( t => t.rows.length > minRequired )
                  .sort( t => t.rows.length , "desc" )
                  .forEach( t =>
                  {
                        count++;
                        dv.el( "div", t.rows[ 0 ].title + " `(" + t.rows.link_src.length + ")` " + "<span class='toc_badpaths_path'>" + t.rows[ 0 ].title_path + "</span>", { cls: "toc_h1" } );
                        dv.el( "div", "  - " + t.rows.list.join("\n-  ") + "", { cls: "toc_h3" } );
                        dv.el( "div", "<br />" );
                  })
      )

    /*
        display NO RESULTS
    */

    if (count === 0)
    {
        const results = dv.el("div", "No Broken Links Found 😊", { cls: "toc_results_none" });
        results.setAttribute("style", "text-align:center;");
    }
```
````

<br />

Next, you need to add some custom CSS.
Open Obsidian Settings, click **Appearance**, and then scroll all the way down. (See image below).

Click the mini folder icon to open your **Obsidian Snippets folder**.

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/install_1.gif"></p>

<br />

Create a new file named whatever (`toc.css` in our example).

Copy the code below and paste it into the new `toc.css` file which should be in `YourVaultName/.obsidian/snippets/toc.css`

<br />

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/install_2.png"></p>

<br />

```css
/*
    table of contents > header 2
*/

    /*
        table of contents > header 2
    */

        .toc_h2 a
        {
            color: #7fa8f5 !important;
            font-size: 10pt;
            font-weight: lighter;
            line-height: 20px;
        }

    /*
        table of contents > header 3
    */

        .toc_h3 a
        {
            color: #f85289 !important;
            font-size: 9pt;
            font-weight: lighter;
            line-height: 20px;
        }

    /*
        table of contents > header 4, 5, 6
    */

        .toc_h4 a, .toc_h5 a, .toc_h6 a
        {
            color: #969696 !important;
            font-size: 8pt;
            font-weight: normal;
            line-height: 20px;
        }

    /*
        table of contents > bad links > path
    */

        .toc_badpaths_path, .toc_badpaths_path a
        {
            color: #adadad !important;
            font-size: 8pt;
            font-weight: normal;
            line-height: 20px;
        }

    /*
        table of contents > no results
    */

        .toc_results_none
        {
            text-align: center;
            font-size: 12pt;
        }
```

<br />

Save the file and go back to **Obsidian Settings** -> **Appearance**. Scroll all the way down and enable the checkbox to the right of `toc.css`.

<p align="center"><img style="width: 100%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-table-of-contents/main/Extras/Bad%20Links/images/install_3.gif"></p>

<br />

You should now see either a list of broken links that exist within your vault, or a happy face and an "All Clear" message.