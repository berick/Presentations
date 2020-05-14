# Angular Ingredients

2020 Evergreen Conference

Bill Erickson

Software Development Engineer

King County Library System

---

# Grid

---

# Dialog

open() vs. onOpen$

---

# FM Editor

---

# Combobox

---

# Org / Org Family Select

---

# Date Pickers

---

# egAccesskey Directive

    !html
    <a routerLink="/staff/circ/patron/search"
      egAccessKey keyCtx="navbar"
      i18n-keySpec keySpec="alt+s f4"
      i18n-keyDesc keyDesc="Patron Search"
      i18n>
      Patron Search
    </a>

---

# Accesskey Help Dialog (ctrl+h)

![egAccessKey Help](media/eg-accesskey-help-dialog.png)

---

# egContextMenu Directive

    !html
    <input
        [egContextMenu]="contextMenuEntries()"
        (menuItemSelected)="contextMenuChange($event.value)"
    />

---

# egContextMenu Directive

![Context Menu](media/context-menu.png)

---

# eg-string Component

## HTML

    !html
    <eg-string #successMsg i18n-text 
        text="Setting Update Succeeded"></eg-string>

## SCRIPT

    !typescript
    @ViewChild('successMsg') successMsg: EgString;
    this.toast.success(this.successMsg.text);

---

# eg-string Component

## HTML

    !html
    <ng-template #searchName let-tab="tab" let-query="query" i18n>
      <ng-container [ngSwitch]="tab">
        <span *ngSwitchCase="'term'">Search:</span>
        <span *ngSwitchCase="'ident'">Identifier:</span>
        <span *ngSwitchCase="'marc'">MARC:</span>
        <span *ngSwitchCase="'browse'">Browse:</span>
      </ng-container> {{query}}
    </ng-template>

    <eg-string key='eg.catalog.recent_search.label' 
        [template]="searchName"></eg-string>

## SCRIPT

    !typescript
    this.strings.interpolate(
        'eg.catalog.recent_search.label',
        {query: query, tab: this.searchTab}
    ).then(txt => this.searchLabel = txt);

---

# Angular 9 $localize in Code

	!typescript
	@Component({
	  template: '{{ title }}'
	})
	export class HomeComponent {
	  title = $localize`You have 10 users`;
	}

> You can then translate the message the same way you would for a       
> template.  But, right now (v9.0.0), the CLI does not extract these    
> messages with the xi18n command as it does for templates.             

Source: https://blog.ninja-squad.com/2019/12/10/angular-localize/

---

# Server Print Templates

---

# Serial vs Parallel requests

Combo of WS and promises practically encourages us to blast many
parallel requests becuase it speeds up the UI.  What we've learned
in the early days of the browser client is this can cause excess 
load on the servers.


	!typescript
    load() {
        this.loading = true;

        this.getThing1()
        .then(_ => this.getThing2())
        .then(_ => this.getThing3())
        .then(_ => this.getThing4())
        .then(_ => this.loading = false);
    }

# Route Reuse

* ngOnInit() is called once per component instantiation
* Components can persist across route changes.
* If a route change should change what data the component uses, it will
  have to be retrieved without the help of ngOnInit
* E.g. Angular catalog record detail navigating results


# Route subiscriptions and custom load()

    !typescript
    ngOnInit() { // this.route === ActivatedRoute

        this.route.paramMap.subscribe((params: ParamMap) => {                  
            // FIRES ON PAGE LOAD
            const recId = +params.get('recordId');
            if (recId !== this.recordId) {
                this.load();
            }
        });
    }

    load() {
        // Reset component state / variables
        // Load data
    }
        


---

# Developer Suggestions

- Symlink en-US directory.

- Create a browser bookmark of the page you are working on
  - JS errors the prevent routing cause the page to jump back to the
    root app page.

- Only implement @Input() set functions when absolutely needed.

- ~/.vimrc option to auto-trim trailing whitespace (ng lint)

    !conf
    " Strip trailing whitespaces from Typescript files
    autocmd BufWritePre *.ts %s/\s\+$//e

- Use 'par'

- ng build --prod can be instructive

---

