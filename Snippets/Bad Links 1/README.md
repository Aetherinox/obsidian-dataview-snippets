# Obsidian: Bad / Unresolved Links - Version 1 <!-- omit from toc -->
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

<br />

---

<br /><br />

## About
The `Page Cloud - Version 1` displays a list of internal links within your vault that lead nowhere, also known as `unresolved links`. 

To fix these, you can either delete the link on the associated page, or you can click each item in the list and create a new page.

Once the link has been fixed, it will be removed from the list.

<br />

This snippet supports `Frontmatter` values for the page names. For more information on this, view the customization section: [Page Titles / Frontmatter Support](#page-titles--frontmatter-support).

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

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_1.png"></p>

<br />

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_2.gif"></p>

<br />

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_3.gif"></p>

<br />

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_4.gif"></p>

<br />

<p align="center"><img style="width: 80%;text-align: center;" src="https://raw.githubusercontent.com/Aetherinox/obsidian-dataview-snippets/main/Snippets/Bad%20Links%201/images/example_5.gif"></p>

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
let pageCurr            = dv.current( );
let file                = app.workspace.getActiveFile( );
let i_total             = 0;
const minRequired       = 0;
const link_filter       = ( a ) => a.toUpperCase( ).replace( /[\s/\\]+/, '' )
const categories        = { 'Full Mode': 'mode-full', 'Compact Mode': 'mode-compact' }

dv.container.className += ' atx-blv1-dataview'

/*
    elements
*/

const divRoot       = dv.el( 'div', '', { container: dv.container, cls: 'atx-blv1-bg' } );
const divSub        = dv.el( 'div', '', { container: divRoot, cls: 'atx-blv1-root' } );
const divHdr        = dv.el( 'div', '', { cls: 'atx-blv1-header', container: divSub } );
const divHdrL       = dv.el( 'div', '', { cls: 'atx-blv1-header-left', container: divHdr } );
const divHdrR       = dv.el( 'div', '', { cls: 'atx-blv1-header-right', container: divHdr } );
const divClear      = dv.el( 'div', '', { cls: 'atx-blv1-ct-clear', container: divHdr } );
const cboModes      = dv.el( 'select', '', { container: divHdrR } );
const hdrTitle      = dv.header( 2, 'Unresolved Links', { container: divHdrL } );
const divBody       = dv.el( 'div', '', { container: divSub, cls: 'atx-blv1-body' } );

/*
    populate select box
*/

for ( const key of Object.keys( categories ) )
{
    const option = dv.el( 'option', key, { container: cboModes } )
    cboModes.appendChild( option )
}

/*
    func > set select box value
*/

async function CboSetValue( obj, set )
{
    for ( var i = 0; i < obj.options.length; i++ )
    {
        if ( obj.options[ i ].text == set )
        {
            obj.options[ i ].selected = true;
            return;
        }
    }
}

CboSetValue( cboModes, dv.current( ).mode );

/*
    func > load ui
*/

async function LoadUI( renderMode )
{
    divBody.innerHTML = '';

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

    if ( renderMode === 'Compact Mode' )
    {
        const elm_spacer            = dv.el( 'div', '<br />', { container: divBody, cls: 'atx-blv1-spacer-compact' } );
        hdrTitle.style.display      = 'none';
    }
    else
    {
        const elm_paragraph         = dv.paragraph( 'The following pages below contain unresolved links and should be fixed. Click each listed note below to create a new page.', { cls: 'atx-blv1-header-about', container: divBody } );
        const elm_spacer            = dv.el( 'div', '<br />', { container: divBody, cls: 'atx-blv1-spacer-full' } );
        hdrTitle.style.display      = 'block';
    }

    const data      = dv.array( arr ).groupBy( t => t.link_src ).where( t => t.rows.length > minRequired ).sort( t => t.rows.length, 'desc' )
    i_total         = data.length;
    let i_curr      = 0;

    dv.list(
        dv.array( data )
                .forEach( t =>
                {
                    const pageArr   = dv.pages( '"' + t.rows[ 0 ].title_path + '"' );
                    var i_items     = Object.keys( t.rows[ 0 ] ).length;
    
                    Promise.all( pageArr.map( async ( b ) =>
                    {
                        const file  = b.file
                        if ( file == null ) return;
    
                        i_curr++;
    
                        const file_name     = file.name || 'Unknown';
                        const file_path     = file.path || '';
                        const file_size     = file.size || 0;
                        const file_title    = file.frontmatter.name || file.frontmatter.title || file.frontmatter.alias || file_name;
                        const file_link     = dv.fileLink( file_path, false, file_title );
                        const file_list     = t.rows.list.join( '\n-  ' );
    
                        dv.el( 'div', file_link, { container: divBody, cls: 'atx-blv1-ct-left' } );
                        dv.el( 'div', t.rows.link_src.length, { container: divBody, cls: 'atx-blv1-ct-right atx-blv1-list-count' } );
                        dv.el( 'div', '', { container: divBody, cls: 'atx-blv1-ct-clear' } );

                        if ( renderMode === 'Full Mode' )
                            dv.el( 'div', file_path, { container: divBody, cls: 'atx-blv1-list-file-path' } );

                        dv.el( 'div', '  - ' + file_list, { container: divBody, cls: 'atx-blv1-list' } );
                        dv.el( 'div', '<br />', { container: divBody } );
            
                        if ( renderMode === 'Full Mode' && i_curr != i_total )
                            dv.el( 'div', '<br />', { container: divBody, cls: 'atx-blv1-spacer' } );
    
                    }))
    
                })
    )

    /*
        no results
    */
    
    if ( i_total === 0 )
    {
        const results = dv.el( 'div', 'No Broken Links Found 😊', { cls: 'atx-blv1-noresults' } );
        results.setAttribute( 'style', 'text-align:center;' );
    }

}

/*
    func > update data
*/

async function UpdateData( selected )
{
    let statusPage      = dv.current( ).mode;
    let setStatus       = 'Full Mode';

    if ( selected && selected != 'undefined' )
        setStatus       = selected;
    else if ( statusPage && statusPage != 'undefined' )
        setStatus       = statusPage;

    app.fileManager.processFrontMatter( file, fm =>
    {
        fm[ 'mode' ] = setStatus;
        LoadUI( setStatus );
    })
}

/*
    select box > on change
*/

cboModes.onchange = function( )
{
    const optSelected = cboModes.value;
    UpdateData( optSelected )
}

/*
    start
*/

UpdateData( );
```
````

<br />

#### Minified Version
This version only formats the settings. All other formatting and comments are removed.

<br />

````shell
```dataviewjs
let pageCurr=dv.current(),file=app.workspace.getActiveFile(),i_total=0;const minRequired=0,link_filter=e=>e.toUpperCase().replace(/[\s/\\]+/,""),categories={"Full Mode":"mode-full","Compact Mode":"mode-compact"};dv.container.className+=" atx-blv1-dataview";const divRoot=dv.el("div","",{container:dv.container,cls:"atx-blv1-bg"}),divSub=dv.el("div","",{container:divRoot,cls:"atx-blv1-root"}),divHdr=dv.el("div","",{cls:"atx-blv1-header",container:divSub}),divHdrL=dv.el("div","",{cls:"atx-blv1-header-left",container:divHdr}),divHdrR=dv.el("div","",{cls:"atx-blv1-header-right",container:divHdr}),divClear=dv.el("div","",{cls:"atx-blv1-ct-clear",container:divHdr}),cboModes=dv.el("select","",{container:divHdrR}),hdrTitle=dv.header(2,"Unresolved Links",{container:divHdrL}),divBody=dv.el("div","",{container:divSub,cls:"atx-blv1-body"});for(const key of Object.keys(categories)){let e=dv.el("option",key,{container:cboModes});cboModes.appendChild(e)}async function CboSetValue(e,t){for(var l=0;l<e.options.length;l++)if(e.options[l].text==t){e.options[l].selected=!0;return}}async function LoadUI(e){divBody.innerHTML="";let t=Object.entries(dv.app.metadataCache.unresolvedLinks).filter(([e,t])=>Object.keys(t).length).flatMap(([e,t])=>Object.keys(t).map(t=>({key:link_filter(t),title:dv.fileLink(e),title_path:e,link_orig:`${dv.fileLink(t)}`,link_src:`${dv.fileLink(e)}`,link_src_orig:`${dv.fileLink(e)} ( ${dv.fileLink(t)} )`,list:`${dv.fileLink(t)}`}))).sort((e,t)=>dv.compare(e.key,t.key));"Compact Mode"===e?(dv.el("div","<br />",{container:divBody,cls:"atx-blv1-spacer-compact"}),hdrTitle.style.display="none"):(dv.paragraph("The following pages below contain unresolved links and should be fixed. Click each listed note below to create a new page.",{cls:"atx-blv1-header-about",container:divBody}),dv.el("div","<br />",{container:divBody,cls:"atx-blv1-spacer-full"}),hdrTitle.style.display="block");let l=dv.array(t).groupBy(e=>e.link_src).where(e=>e.rows.length>0).sort(e=>e.rows.length,"desc");i_total=l.length;let i=0;if(dv.list(dv.array(l).forEach(t=>{let l=dv.pages('"'+t.rows[0].title_path+'"');Object.keys(t.rows[0]).length,Promise.all(l.map(async l=>{let o=l.file;if(null==o)return;i++;let a=o.name||"Unknown",n=o.path||"";o.size;let r=o.frontmatter.name||o.frontmatter.title||o.frontmatter.alias||a,d=dv.fileLink(n,!1,r),c=t.rows.list.join("\n-  ");dv.el("div",d,{container:divBody,cls:"atx-blv1-ct-left"}),dv.el("div",t.rows.link_src.length,{container:divBody,cls:"atx-blv1-ct-right atx-blv1-list-count"}),dv.el("div","",{container:divBody,cls:"atx-blv1-ct-clear"}),"Full Mode"===e&&dv.el("div",n,{container:divBody,cls:"atx-blv1-list-file-path"}),dv.el("div","  - "+c,{container:divBody,cls:"atx-blv1-list"}),dv.el("div","<br />",{container:divBody}),"Full Mode"===e&&i!=i_total&&dv.el("div","<br />",{container:divBody,cls:"atx-blv1-spacer"})}))})),0===i_total){let o=dv.el("div","No Broken Links Found \uD83D\uDE0A",{cls:"atx-blv1-noresults"});o.setAttribute("style","text-align:center;")}}async function UpdateData(e){let t=dv.current().mode,l="Full Mode";e&&"undefined"!=e?l=e:t&&"undefined"!=t&&(l=t),app.fileManager.processFrontMatter(file,e=>{e.mode=l,LoadUI(l)})}CboSetValue(cboModes,dv.current().mode),cboModes.onchange=function(){let e=cboModes.value;UpdateData(e)},UpdateData();
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
/* @settings
name: 🔗 Bad Links - Version 1
id: atx-blv1
settings:
-
    id:                     atx-blv1-cat-general
    title:                  '1. Appearance'
    description:            'General appearance settings'
    type:                   heading
    level:                  1
    collapsed:              true


-
        id:                 atx-blv1-bg-about
        title:              ''
        description:        "<br /><br /><h1>Background</h1>Settings to adjust the background for the main panel."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-bg-custom-enabled
        title:              Activate Custom Background Image
        type:               class-toggle
        default:            true
-
        id:                 atx-blv1-bg-clr
        title:              'Background Color'
        description:        'Main interface background (only visible if custom background disabled)'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgba( 41, 41, 41, 1 )'
        default-dark:       'rgba( 41, 41, 41, 1 )'
-
        id:                 atx-blv1-bg-custom-image
        title:              'Background Image (url)'
        description:        'Customize the background image. Use the format in the Default below for an Online image.'
        type:               variable-text
        default:            'url("https://")'
-
        id:                 atx-blv1-bg-custom-opacity
        title:              'Background Image Opacity'
        description:        'Background image alpha / opacity'
        type:               variable-number-slider
        default:            0.4
        min:                0
        max:                1
        step:               0.01
        format:             ""
-
        id:                 atx-blv1-bg-width
        title:              'Background / Table Width'
        description:        'Width for the entire box'
        type:               variable-number-slider
        default:            75
        min:                0
        max:                100
        step:               1
        format:             "%"
-
        id:                 atx-blv1-bg-border-clr
        title:              'Border & Separator Color'
        description:        'Color for border and separators.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgba( 255, 255, 255, 0.4 )'
        default-dark:       'rgba( 255, 255, 255, 0.4 )'


-


        id:                 atx-blv1-general-about
        title:              ''
        description:        "<br /><br /><h1>General</h1>General settings not categorized anywhere else.<br /><br />`None Yet`"
        type:               info-text
        markdown:           true


-


        id:                 atx-blv1-header-about
        title:              ''
        description:        "<br /><br /><h1>Header</h1>The following settings adjust the header of the interface."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-header-font-size
        title:              'Description Font Size'
        description:        'Font size for header description text.'
        type:               variable-number-slider
        default:            13
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                  atx-blv1-header-font-clr
        title:              'Description Font Color'
        description:        'Font color for header description text.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 182, 182, 182, 1 )'
        default-dark:       'rgb( 182, 182, 182, 1 )'


-


        id:                 atx-blv1-list-subj-notename-about
        title:              ''
        description:        "<br /><br /><h1>Unresolved Note Name</h1>The following settings adjust the note name."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-list-subj-notename-font-size
        title:              'Font Size'
        description:        'Font size for page titles in unresolved list.'
        type:               variable-number-slider
        default:            17
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-blv1-list-subj-notename-font-clr-n
        title:              'Font Color (Normal)'
        description:        'Font color for page titles in unresolved list.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 255, 85, 125, 1 )'
        default-dark:       'rgb( 255, 85, 125, 1 )'
-
        id:                 atx-blv1-list-subj-notename-font-clr-h
        title:              'Font Color (Hover)'
        description:        'Font color for page titles in unresolved list.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 255, 204, 255, 1 )'
        default-dark:       'rgb( 255, 255, 255, 1 )'


-


        id:                 atx-blv1-list-subj-filename-about
        title:              ''
        description:        "<br /><br /><h1>Unresolved File Name</h1>The following settings adjust file path listed below each note."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-list-subj-filename-font-size
        title:              'Font Size'
        description:        'Font size for page file.'
        type:               variable-number-slider
        default:            12
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-blv1-list-subj-filename-font-clr
        title:              'Font Color'
        description:        'Font color for page file.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 180, 180, 180, 1 )'
        default-dark:       'rgb( 180, 180, 180, 1 )'
-
        id:                 atx-blv1-list-subj-filename-padding-about
        title:              'Padding'
        description:        "Different themes use different padding. Use this setting to adjust the position of the file name shown below each note.<br /><br /> Order of values: `top, right, bottom, left`"
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-list-subj-filename-padding
        title:              ''
        description:        ''
        type:               variable-text
        markdown:           true
        default:            '0px 0px 10px 0px'


-


        id:                 atx-blv1-list-unresolved-about
        title:              ''
        description:        "<br /><br /><h1>Unresolved File List</h1>The following settings adjust the list of unresolved items displayed below each note title."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-list-unresolved-font-size
        title:              'Font Size'
        description:        'Font size for page titles in unresolved list.'
        type:               variable-number-slider
        default:            12
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-blv1-list-unresolved-font-clr-n
        title:              'Font Color (Normal)'
        description:        'Font color for page titles in unresolved list.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 255, 255, 255, 1 )'
        default-dark:       'rgb( 255, 255, 255, 1 )'
-
        id:                 atx-blv1-list-unresolved-font-clr-h
        title:              'Font Color (Hover)'
        description:        'Font color for page titles in unresolved list.'
        type:               variable-themed-color
        opacity:            true
        format:             rgb
        default-light:      'rgb( 239, 84, 113, 1 )'
        default-dark:       'rgb( 239, 84, 113, 1 )'


-


        id:                 atx-blv1-list-counter-about
        title:              ''
        description:        "<br /><br /><h1>Issue Counter</h1>The following settings adjust the issue counter on the right side of each issue inside the pulsing circle."
        type:               info-text
        markdown:           true
-
        id:                 atx-blv1-list-counter-circle-clr
        title:              'Circle Color'
        description:        'Background color for issue circle.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#a32b5d'
        default-dark:       '#a32b5d'
-
        id:                 atx-blv1-list-counter-font-size
        title:              'Font Size'
        description:        'Font size for issue count indicator.'
        type:               variable-number-slider
        default:            12
        format:             px
        min:                0
        max:                30
        step:               1
-
        id:                 atx-blv1-list-counter-font-clr
        title:              'Font Color'
        description:        'Font color for issue count indicator.'
        type:               variable-themed-color
        opacity:            true
        format:             hex
        default-light:      '#FFFFFF'
        default-dark:       '#FFFFFF'


-


    id:                     atx-blv1-cat-anim
    title:                  '2. Animations'
    description:            'Animation settings'
    type:                   heading
    level:                  1
    collapsed:              true
-
        id:                 atx-blv1-cat-anim-1
        title:              'Ripple Animation'
        description:        'Play animation around issue count indicator'
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
        id:                 atx-blv1-anim-clr-r
        title:              'Ripple Color (Red)'
        description:        'Individual Red value for the ripple color'
        type:               variable-text
        default:            "255"
-
        id:                 atx-blv1-anim-clr-g
        title:              'Ripple Color (Green)'
        description:        'Individual Green value for the ripple color'
        type:               variable-text
        default:            "255"
-
        id:                 atx-blv1-anim-clr-b
        title:              'Ripple Color (Blue)'
        description:        'Individual Blue value for the ripple color'
        type:               variable-text
        default:            "255"


-


    id:                     atx-blv1-cat-support
    title:                  '3. Support'
    description:            'Links associated to this snippet'
    type:                   heading
    level:                  1
    collapsed:              true
-
    id:                     atx-blv1-support-updates
    title:                  View Updates
    description:            "[https://github.com/Aetherinox/obsidian-dataview-snippets](https://github.com/Aetherinox/obsidian-dataview-snippets)"
    type:                   info-text
    markdown:               true
-

*/

    /*
        Import
    */

        @import url(http://fonts.googleapis.com/css?family=Open+Sans);

    /*
        animation: ripple
    */

        @keyframes atx-blv1-anim-ripple
        {
            0%
            {
                box-shadow:             0 0 0 0 rgba( var( --color_ripple ), 0.9 );
            }

            20%, 70%
            {
                box-shadow:             0 0 0 11px rgba( var( --color_ripple ), 0 );
            }

            100%
            {
                box-shadow:             0 0 0 0 rgba( var( --color_ripple ), 0 );
            }
        }

    /*
        Snippet: Bad Links - Version 1
    */

        body
        {
            --atx-blv1-anim-clr-r:                      255;
            --atx-blv1-anim-clr-g:                      255;
            --atx-blv1-anim-clr-b:                      255;
            --color_ripple:                             var( --atx-blv1-anim-clr-r ), var( --atx-blv1-anim-clr-g ), var( --atx-blv1-anim-clr-b );
            --atx-blv1-anim-delay:                      2s;
            --atx-blv1-anim-1:                          atx-blv1-anim-ripple;
            --atx-blv1-bg-custom-image:                 url("https://r1.ilikewallpaper.net/ipad-pro-wallpapers/download/100031/dark-blur-abstract-4k-ipad-pro-wallpaper-ilikewallpaper_com.jpg");
            --atx-blv1-bg-custom-opacity:               0.4;
            --atx-blv1-bg-width:                        75%;
            --atx-blv1-bg-clr:                          rgba( 41, 41, 41, 1 );
            --atx-blv1-bg-border-clr:                   rgba( 255, 255, 255, 0.2 );

            --atx-blv1-list-counter-font-size:          12px;
            --atx-blv1-list-counter-circle-clr:         #a32b5d;
            --atx-blv1-list-counter-font-clr:           #FFFFFF;

            --atx-blv1-list-subj-notename-font-size:    17px;
            --atx-blv1-list-subj-notename-font-clr-n:   rgb( 255, 85, 125, 1 );
            --atx-blv1-list-subj-notename-font-clr-h:   rgb( 255, 255, 255, 1 );

            --atx-blv1-list-unresolved-font-size:       12px;
            --atx-blv1-list-unresolved-font-clr-n:      rgb( 255, 255, 255, 1 );
            --atx-blv1-list-unresolved-font-clr-h:      rgb( 239, 84, 113, 1 );

            --atx-blv1-list-subj-filename-font-size:    12px;
            --atx-blv1-list-subj-filename-font-clr:     rgb( 180, 180, 180, 1 );
            --atx-blv1-list-subj-filename-padding:      0px 0px 10px 0px;

            --atx-blv1-header-font-size:                13px;
            --atx-blv1-header-font-clr:                 rgb( 182, 182, 182, 1 );
        }

    /*
        Settings > Animations
    */

        body.theme-light.anim-1-disabled,
        body.theme-dark.anim-1-disabled
        {
            --atx-blv1-anim-1:          none;
        }
        
        body.theme-light.anim-1-enabled,
        body.theme-dark.anim-1-enabled
        {
            --atx-blv1-anim-1:          atx-blv1-anim-ripple;
        }

        body.colorful-link-animation :is(.markdown-preview-view,.markdown-rendered) a:hover
        {
            animation:                  none !important; 
        }

        body:not( .atx-blv1-bg-custom-enabled )
        {
            --atx-blv1-bg-custom-image: none;
        }

    /*
        Dataview parent div
    */

        .atx-blv1-dataview
        {
            border-radius:              6px;
            border:                     1px dashed #444444;
            width:                      var( --atx-blv1-bg-width );
            padding:                    0px;
            margin:                     0 auto;
        }

    /*
        Header
    */

        .atx-blv1-header-left
        {
            float:                      left;
        }

        .atx-blv1-header-right
        {
            float:                      right;
        }

        .atx-blv1-header-right > select
        {
            cursor:                     pointer;
            margin-top:                 10px;
        }

        .atx-blv1-header-about
        {
            font-size:                  var( --atx-blv1-header-font-size );
            color:                      var( --atx-blv1-header-font-clr );
        }

    /*
        Generic Containers
    */

        .atx-blv1-ct-left
        {
            float:                      left;
            width:                      75%;
        }
        
        .atx-blv1-ct-right
        {
            float:                      right;
            width:                      10%;
        }

        .atx-blv1-ct-clear
        {
            clear:                      both;
        }

    /*
        root > background
    */

        .atx-blv1-bg::before
        {
            content:                    "";
            background-color:           var( --atx-blv1-bg-clr );
            background-image:           var( --atx-blv1-bg-custom-image );
            background-position:        center;
            background-repeat:          no-repeat;
            background-size:            cover;
            position:                   absolute;
            top:                        50%;
            left:                       50%;
            transform:                  translate( -50%, -50% );
            width:                      var( --atx-blv1-bg-width );
            height:                     100%;
            opacity:                    var( --atx-blv1-bg-custom-opacity );
            margin:                     0 auto;
            z-index:                    -1;
        }

    /*
        root
    */

        .atx-blv1-root
        {
            padding:                    20px;
            padding-top:                10px;
        }

        .atx-blv1-ct-left > span > a
        {
            color:                      var( --atx-blv1-list-subj-notename-font-clr-n ) !important;
            font-weight:                normal !important;
            font-size:                  var( --atx-blv1-list-subj-notename-font-size );
        }

        .atx-blv1-ct-left > span > a:hover
        {
            color:                      var( --atx-blv1-list-subj-notename-font-clr-h ) !important;
            background:                 none;
            cursor:                     pointer;
        }

        .atx-blv1-list a
        {
            color:                      var( --atx-blv1-list-unresolved-font-clr-n ) !important;
            font-weight:                normal;
            font-size:                  var( --atx-blv1-list-unresolved-font-size );
        }

        .atx-blv1-list a:hover
        {
            color:                      var( --atx-blv1-list-unresolved-font-clr-h ) !important;
            font-weight:                normal;
            font-size:                  var( --atx-blv1-list-unresolved-font-size );
            background:                 none;
        }

    /*
        issue path
    */

        .atx-blv1-list-file-path
        {
            font-size:                  var( --atx-blv1-list-subj-filename-font-size );
            padding:                    var( --atx-blv1-list-subj-filename-padding );
            color:                      var( --atx-blv1-list-subj-filename-font-clr );
        }

    /*
        Spacers
    */

        .atx-blv1-spacer
        {
            border-top:                 1px dashed var( --atx-blv1-bg-border-clr );
        }

        .atx-blv1-spacer-full
        {
            border-top:                 1px dashed var( --atx-blv1-bg-border-clr );
        }

        .atx-blv1-spacer-compact
        {
            margin-top:                 20px;
            border-top:                 1px dashed var( --atx-blv1-bg-border-clr );
        }

    /*
        root > count issues
    */

        .atx-blv1-list-count
        {
            border-radius:              50%;
            background:                 var( --atx-blv1-list-counter-circle-clr );
            color:                      var( --atx-blv1-list-counter-font-clr );
            font-weight:                bold !important;
            text-align:                 center;
            font:                       var( --atx-blv1-list-counter-font-size ) sans-serif;
            vertical-align:             middle;
            margin:                     auto 0;
            line-height:                22px;
            transform:                  translateX(-50%);
            width:                      22px !important;
            height:                     22px !important;
            animation-name:             var( --atx-blv1-anim-1 );
            animation-duration:         6s;
            animation-timing-function:  ease;
            animation-iteration-count:  infinite;
            animation-direction:        normal;
        }

    /*
        no results
    */

        .atx-blv1-noresults
        {
            padding-bottom: 35px;
            font-size: 16px;
            font-family: "Open Sans";
        }
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
This script supports `Frontmatter` / `Metadata` titles.