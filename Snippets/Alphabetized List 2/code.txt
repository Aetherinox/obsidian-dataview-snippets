dv.container.className += ' atx-alv1-dataview'

var html                = "";
let arrAlphabet         = [];
let arrPages            = dv.pages( "" )
.forEach( p =>
{
    const file          = p.file
    const file_path     = file.path;
    const file_name     = file.name;
    const file_label    = file.frontmatter.name || file.frontmatter.title || file.frontmatter.alias || file_name;

    const letter        = file_label.charAt( 0 ).toUpperCase( );
    let index           = arrAlphabet.findIndex( ( item ) => item.name === letter );

    if ( index === -1 )
        arrAlphabet.push( { name: letter, pages: [ { name: file_name, label: file_label, path: file_path } ] } );
    else
    {
        var item        = arrAlphabet.find( item => item.name == letter );
        let arr         = item.pages;

        arr.push( { name: file_name, label: file_label, path: file_path } );
    }

    arrAlphabet.sort( ( a, b ) => a.name.localeCompare( b.name ) )
});

const ulAlphabet = dv.el( 'ul', '', { container: dv.container } );

dv.list(
    dv.array( arrAlphabet )
        .forEach( obj =>
        {
            const arrPages          = obj.pages;
            const liAlphabet        = dv.el( 'li', obj.name, { container: ulAlphabet } );
            const ulPages           = dv.el( 'ul', '', { container: liAlphabet });

            Promise.all( arrPages.map( async ( pages ) =>
            {
                const page_path     = pages.path;
                const page_name     = pages.name;
                const page_label    = pages.label;

                const file_link     = dv.fileLink( page_path, false, page_label );
                
                dv.el( 'li', file_link, { container: ulPages } );

            }
            ));
        })
)

const divClose = dv.el( 'div', html, { container: dv.container, cls: 'atx-alv1-close' } );