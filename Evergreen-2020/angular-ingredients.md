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

TODO: screen cap of ctrl+h window

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

# eg-string Component

- Eventually replaced with $localize / Angular 9+

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

