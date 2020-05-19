# labelset
Just a JS script to paste in the browser console to create and update label my label on each of my repositories.

## Usage

The original script was taken here : https://douglascayers.com/2019/08/01/how-to-export-and-import-github-issue-labels-between-projects/

To create and/or update your labels you just have to paste this script in your repository label page : 

```js
/**
 * Inspired by @Isaddo original script: https://gist.github.com/Isaddo/7efebcb673a0957b9c6f07cd14826ea4
 * Adds descriptions per @NillerMedDild https://gist.github.com/Isaddo/7efebcb673a0957b9c6f07cd14826ea4#gistcomment-2715349
 * 
 * Changes include:
 *  - CSS selectors use `js` prefix
 * 
 * Last tested 2019-July-27:
 *  - Chrome 75.0.3770.142
 *  - Safari 12.1.2
 *  - macOS 10.14.6
 */
function createLabel( label ) {
    document.querySelector( '.js-new-label-name-input' ).value = label.name;
    document.querySelector( '.js-new-label-description-input' ).value = label.description;
    document.querySelector( '.js-new-label-color-input' ).value = '#' + label.color;
    document.querySelector( '.js-details-target ~ .btn-primary' ).disabled = false;
    document.querySelector( '.js-details-target ~ .btn-primary' ).click();
}

function updateLabel( label ) {
    let updatedLabel = false;
    [].slice.call( document.querySelectorAll( '.js-labels-list-item' ) ).forEach( element => {
        if ( element.querySelector( '.js-label-link' ).textContent.trim() === label.name ) {
            updatedLabel = true;
            element.querySelector( '.js-edit-label' ).click();
            element.querySelector( '.js-new-label-name-input' ).value = label.name;
            element.querySelector( '.js-new-label-description-input' ).value = label.description;
            element.querySelector( '.js-new-label-color-input' ).value = '#' + label.color;
            element.querySelector( '.js-edit-label-cancel ~ .btn-primary' ).click();
        }
    });
    return updatedLabel;
}

function createOrUpdate( label ) {
    if ( !updateLabel( label ) ) {
        createLabel( label );
    }
}
    // YOUR LABELS JSON HERE
[
    {
        "name": "breaking-change",
        "description": "Dramatic change (interface, data model or other) that impacts the understanding of the app",
        "color": "e27c68"
    },
    {
        "name": "bug",
        "description": "Something isn't working",
        "color": "d73a4a"
    },
    {
        "name": "bugfix",
        "description": "Fix some bugs",
        "color": "bf415a"
    },
    {
        "name": "cleaning",
        "description": "Internal cleaning of the application or design",
        "color": "50e587"
    },
    {
        "name": "code refactor",
        "description": "Refactoring of the code (use CodeFactor)",
        "color": "85e079"
    },
    {
        "name": "critical",
        "description": "High-priority issues impacting the product to be treated immediately",
        "color": "d65392"
    },
    {
        "name": "documentation",
        "description": "Improvements or additions to documentation",
        "color": "0075ca"
    },
    {
        "name": "duplicate",
        "description": "This issue or pull request already exists",
        "color": "cfd3d7"
    },
    {
        "name": "feature",
        "description": "New feature or request",
        "color": "a2eeef"
    },
    {
        "name": "help wanted",
        "description": "Extra attention is needed",
        "color": "008672"
    },
    {
        "name": "invalid",
        "description": "This doesn't seem right",
        "color": "e4e669"
    },
    {
        "name": "maintenance",
        "description": null,
        "color": "5319e7"
    },
    {
        "name": "poc",
        "description": "Proof of Concept",
        "color": "bfd0fc"
    },
    {
        "name": "question",
        "description": "Further information is requested",
        "color": "d876e3"
    },
    {
        "name": "to-be-announced",
        "description": "When it's time to notify users who had requested the functionality or modification",
        "color": "f49ca4"
    },
    {
        "name": "unknown",
        "description": "Good for newcomers",
        "color": "7057ff"
    },
    {
        "name": "wontfix",
        "description": "This will not be worked on",
        "color": "ffffff"
    }
].forEach( label => createOrUpdate( label ) );
```

## Export

If you want to get your own label from your organisation page, just run this one from the org label page : 
```js
/**
 * Inspired by @MoOx original script: https://gist.github.com/MoOx/93c2853fee760f42d97f
 * Adds file download per @micalevisk https://gist.github.com/MoOx/93c2853fee760f42d97f#gistcomment-2660220
 * 
 * Changes include:
 *  - Get the description from the `title` attribute instead of `aria-label` (doesn't exist anymore)
 *  - Use style.backgroundColor and parse the rgb(...) to hex (rather than regex parsing of 'style' string)
 *  - Downloads labels to a JSON file named after the webpage to know which GitHub repo they came from.
 * 
 * Last tested 2019-July-27:
 *  - Chrome 75.0.3770.142
 *  - Safari 12.1.2
 *  - macOS 10.14.6
 */
function exportGitHubLabels() {
    let labels = [];
    [].slice.call( document.querySelectorAll( '.js-label-link' ) )
    .forEach( element => {
        labels.push({
            name: element.textContent.trim(),
            description: element.getAttribute( 'title' ),
            color: (
                // style.backgroundColor returns "rgb(...)"
                // but GitHub expects hex when we import the colors
                element.style.backgroundColor
                // grab between 'rgb(' and ')'
                .substring( 4, element.style.backgroundColor.length - 1 )
                // convert comma-delimited string into array of three numbers
                .split( ',' )
                // reduce array of three numbers into single hex color 
                .reduce( ( hexValue, rgbValue ) => {
                    return (
                        // append next two-digit hex value
                        hexValue +
                        // convert decimal to hex
                        Number( rgbValue ).toString( 16 )
                        // each number in a hex color is two characters
                        .padStart( 2, '0' )
                    );
                }, '' )
            )
        });
    });
    return labels;
}

function saveDataAsJSON( data, filename ) {
    const blob = new Blob( [ JSON.stringify( data, null, 4 ) ], { type: 'text/json' } );
    const a = document.createElement( 'a' );
    a.download = filename;
    a.href = window.URL.createObjectURL( blob );
    a.dataset.downloadurl = [ 'text/json', a.download, a.href ].join( ':' );
    a.click();
}

saveDataAsJSON( exportGitHubLabels(), document.title + '.json' );
```
