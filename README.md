
![](https://flat.badgen.net/badge/icon/NESTED,FRAMES?list=%20&icon=https://www.svgrepo.com/show/231196/file-document.svg&label&scale=2&color=22272e&labelColor=04C38E)

* <b>CHALLENGE&nbsp;&nbsp;&nbsp;&nbsp;</b> Application was creating a new frameset each time you navigated to a main page. Cypress does not support frames natively, a temporary workaround was needed until devs fixed the issue.
* <b>SOLUTION&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</b> Recursive selector for variable number of nested frames.

```typescript
function loopFrames(frame) {
  return cy.get(frame).then((el) => {
    const rootdiv = el.contents().find('[id="root"]'); // we check if root div
    const nestedframe = el.contents().find('frame[name="Detail"]'); // and another frame are present
    if (nestedframe.length > 0) loopFrames(nestedframe); // and recall function if we find another frame
    else cy.wrap(rootdiv); // finally, we return the rootdiv under the last frame
    });
}
```

<details> <summary><code>&nbsp;frameset HTML snippet&nbsp;</code></summary>

```html
#document
  <html>
    <head>...</head>
    <frameset rows="0,*" cols="*">
      <frame name="bottom" src="./Common/blank.htm">
      <frame name="Detail" src="./BN/sigin?loginErr=">
        #document
          <html>
            <head>...</head>
            <frameset rows="0,*" cols="*">
              <frame name="bottom" src="./Common/blank.htm">
              <frame name="Detail" src="./BN/1E/?myRn=">
                #document
                  <html>
                    <head>...</head>
                    <frameset rows="0,*" cols="*">
                      <frame name="bottom" src="./Common/blank.htm">
                      <frame name="Detail" src="./BN/2E/?myRn=">
                        #document
                          <html>
                            <head>...</head>
                            <frameset rows="0,*" cols="*">
                              <frame name="bottom" src="./Common/blank.htm">
                              <frame name="Detail" src="./BN/1E/?myRn=">
                                #document
                                  <!DOCTYPE html>
                                  <html>
                                    <head>...</head>
                                    <body>
                                      <div id="root">
```
</details>

---

![](https://flat.badgen.net/badge/icon/TRAVERSAL,LOCATORS?list=%20&icon=https://www.svgrepo.com/show/46599/stairs.svg&label&scale=2&color=22272e&labelColor=04C38E)

* <b>CHALLENGE&nbsp;&nbsp;&nbsp;&nbsp;</b> Application had hundreds of forms and wizards each with up to dozens of input fields. Identifying each field for each page, form and field for automation was taking a long time.
* <b>SOLUTION&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</b> Traversal locators anchoring on label elements syncing with manual test defintions. Writing automated scripts no longer required an inspection of html tags and manual tests could be quickly translated to automation logic.
    
```typescript
function inputByLabel(label: string, input: string) {
  cy.iframe('iframe.product-frame')
    .contains('label', label) // anchor label element
    .parentsUntil('ub-form-group') // go up the tree till a common parent with the input field
    .find('input[type="text"]')
    .should('be.visible')
    .type(input);
}
```

<details>
<summary><code>&nbsp;input HTML snippet&nbsp;</code></summary>

```html
<ub-form-group>
  #shadow-root (open)
    <div class="css-exg1y7">
      <div class="css-13et6b">...</div> <!-- icon -->
      <div class="css-1ax517"> <!-- label -->
        <ub-text type="label">
          #shadow-root (open)
            <label> "Sample Label" </label>
        </ub-text>
      </div>
      <div class="css=16v52f"> <!-- input -->
        <div class="css-4cf88t">
          <div class="css-16v3hw">
            <ub-edit-field editor="textbox" value="Sample Value">
              #shadow-root (open)
                <div class="ub-edit-field__container">
                  <input type="text">
                </div>
```

</details>

```typescript
function selectByLabel(label: string, option: string) {
  cy.iframe('iframe.product-frame')
    .contains('label', label) // anchor label element
    .parentsUntil('ub-form-group') // go up the tree till a common parent with the dropdown
    .find('[class="ub-combobox-icon"]')
    .should('be.visible')
    .click() // expand dropdown
    .parentsUntil('ub-form-group')
    .find('ub-popover')
    .should('be.visible')
    .contains(option)
    .click({ scrollBehavior: 'center' }) // scroll option into view and click it
    .blur(); // defocus to trigger saving of option in field
}
```

<details>
<summary><code>&nbsp;dropdown HTML snippet&nbsp;</code></summary>

```html
<ub-form-group>
  #shadow-root (open)
    <div class="css-exg1y7">
      <div class="css-13et6b">...</div> <!-- icon -->
      <div class="css-1ax517">...</div> <!-- label -->
        <ub-text type="label">
          #shadow-root (open)
            <label> "Sample Label" </label>
        </ub-text>
      </div>
      <div class="css=16v52q"> <!-- dropdown -->
        <div class="css=4cf88t">
          <div class="css=16v52q">
            <ub-edit-field>
              #shadow-root (open)
                <div>
                  <div class="ub-edit-field__container">
                    <ub-combobox>
                      #shadow-root (open)
                        <div class="ub-combobox__container">
                        <ub-popover>
                          #shadow-root(open)
                          <div class="ub-combobox_list">
                          <ub-list>
                            #shadow-root(open)
                              <div class="ub-combobox__list">
                                <ul>
                                  <li>
```

</details>

---

![](https://flat.badgen.net/badge/icon/WRAPPER,FUNCTIONS?list=%20&icon=https://www.svgrepo.com/show/287353/pills-pill.svg&label&scale=2&color=22272e&labelColor=04C38E)

* <b>CHALLENGE&nbsp;&nbsp;&nbsp;&nbsp;</b> Faster and streamlined data input and assertion for forms and wizards
* <b>SOLUTION&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</b> Helper input and assertion functions that take objects defining all data as parameters

<details> <summary><code>&nbsp;interface and types&nbsp;</code></summary>

```typescript
type fieldType = 'input' | 'dropdown' | 'date' | 'button';
type fieldAction  = 'click' | 'type' | 'select';

interface IField {
  Type: fieldType,
  Locator: string,
  Action: fieldAction,
  Context?: string,
}

type FieldObj = {[key: string]: IField};
```
</details>

<details> <summary><code>&nbsp;object with data and actions&nbsp;</code></summary>

```typescript
var Form : FieldObj = {
  'First Name': { Type: 'input', Action: 'type', Context: 'user has typed' },
  'Last Name': { Type: 'input', Action: 'type', Context: 'user has typed' },
  'Next': { Type: 'button', Action: 'click' },
  'Location': { Type: 'dropdown', Action: 'select', Context: 'user has selected' },
}
```
</details>

```typescript
function FillForm(obj: Interface) {
  cy.fixture(file).then((org: IOrganisation) => {
    for (const [label, value] of Object.entries(org)) {
      EstatesManager.Org_InputbyLabel(label, clear + value);
    }
    // EstatesManager.Org_SaveButton('enabled', 'click');
    EstatesManager.Org_ClickHomeIcon();
    EstatesManager.Org_SaveButton('enabled', 'click');
  });
}
``` 
