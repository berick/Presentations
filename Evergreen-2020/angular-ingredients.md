# Angular Ingredients

2020 Evergreen Conference

Bill Erickson

Software Development Engineer

King County Library System

---

# Grid

---

# Dialog

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

# Developer Suggestions

- Symlink en-US directory.

- Always create a browser bookmark of the page you are working on
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

