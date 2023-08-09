# New Devs Tutorial Bits and Bobs

## Grid Cell Templates

The EG Angular grid typically renders simple text content (strings, numbers)
within each cell of the grid.  The values may come from a variety of sources
(key/value pairs, objects with functions, etc.), but the end result is typically
a simple string value.

The one exception to this rule are grid cell templates.  Cell templates
allow the grid user to render HTML content within a grid cell instead of
a simple string value.  A common example of how this is useful is rendering
a patron barcode as an HTML link instead of plain text, so the user can
click on the barcode to open the patron account.

### Defining Templates

A cell template is defined within the Angular markup, typically in the same 
.html file as the grid it belongs to.  Each template gets a name and some 
HTML content.  

This template is named "barcodeTemplate".  It verifies each rendered patron
has a "card" value (via \*ngIf), then renders the patron barcode as a link:

```html
<ng-template #barcodeTemplate let-patron="row">                                   
  <ng-container *ngIf="patron.card()">                                            
    <a routerLink="/staff/circ/patron/{{patron.id()}}/checkout">                  
      {{patron.card().barcode()}}                                                 
    </a>                                                                     
  </ng-container>                                                            
</ng-template>      
```

#### Link The Template to Its Grid Cell

Let the grid know which grid column should use the template to
render its content.

```html
<eg-grid-column name="barcode" 
  [cellTemplate]="barcodeTemplate" label="Barcode" i18n-label>                                              
</eg-grid-column>       
```

#### Template Context and Variables

Cell templates are rendered by the grid, so each template only has 
access to data that the grid also has access to.  The main (and typicall 
only) variable needed when defining a template is the "row" variable. 
The "row" will match the data stored by the grid within each row.  For a 
grid of patrons, each "row" will be a patron object or a JS object which
contains patron data.  

> [!NOTE]
> A grid row can be pretty much any data structure and they vary per grid.

To access the values stored in each row, assign a template-local variable
to contain the row contents.  In this example, we assign the "row" value
to a template-local variable called "patron" using the `let-*` syntax:

```html
<ng-template #barcodeTemplate let-patron="row">
```

> [!NOTE]
> A common approach is to say `let-row="row"` since it's clear and tidy.

Once assigned, the variable may be used in the template like any other
Angular variable.

```html
<ng-template #myTemplate let-patron="row">                                   
  <span i18n>
    Hello {{patron.pref_first_given_name() || patron.first_given_name()}}!
  </span>
</ng-template>
```


### Printing and Exporting Cell Template Content

Printing or exporting a grid as CSV requires the cell contents be
plain text.  Since cell templates contain rendered HTML content, the
print/export function needs a way to get a plain-text version of the
cell content.

Defining a plain text alternative for a cell template requires telling
the grid how to find the data you wish to use in lieu of the 
renderd HTML content.

* Define a text generator in the grid definition in the HTML markup.
```html
<eg-grid [cellTextGenerator]="cellTextGenerator" ...>
    ...
</eg-grid>
```
* Define the cell text generator in the Typescript:
```ts
import {GridCellTextGenerator} from '@eg/share/grid/grid';

// ...

export class MyClass implements OnInit {
  cellTextGenerator: GridCellTextGenerator;

  ngOnInit() {
    this.cellTextGenerator = {
      /*
      "barcode" must match the "name" attribute for the 
      <eg-grid-column/> that uses the cell template.
      
      "row" is the same row value used within the cell template.
     
      Once again, this code checks to ses if the patron (here, "row")
      has a "card" value then returns the barcode as the
      cell text if present, or empty string otherwise.
      */
      barcode: row => row.card() ? row.card().barcode() : ''

      // Entries for other cell templates live here.
    };
  }
}
```




