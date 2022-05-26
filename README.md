---
<details open>
<summary>
<h1>
&nbsp; <img src="https://img.icons8.com/wired/42/04C38E/activity-feed.png"> &nbsp; Nested frames
</h1>
</summary>
  
---

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

<details>
<summary>HTML</summary>

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
</details>
             
---
<details open>
<summary>
<h1>
&nbsp; <img src="https://img.icons8.com/material/42/04C38E/stairs-up.png" width="42" height="42"> &nbsp; Traversal Locators
</h1>
</summary>
  
---
	
* <b>CHALLENGE&nbsp;&nbsp;&nbsp;&nbsp;</b> Application had hundreds of forms and wizards each with up to dozens of input fields. Identifying each field for each page>form>field for automation was taking a long time.
* <b>SOLUTION&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</b> Traversal locators anchoring on label elements syncing with manual test defintions. Writing automated scripts no longer required an inspection of html tags and manual tests could be quickly translated to automation logic.
	
```typescript
function inputByLabel(label: string, input: string) {
  cy.iframe('iframe.product-frame')
    .contains('label', label) // anchor label element
    .parentsUntil('ub-form-group') // go up the tree with till a common parent with the input field
    .find('input[type=text]')
    .should('be.visible')
    .type(input);
}
```

<details>
	<summary>HTML</summary>

```html
<ub-form-group>
  #shadow-root (open)
    <div class="css-exg1y7">
      <div class="css-13et6b">...</div> // <<< icon
      <div class="css-1ax517"> // <<< label
        <ub-text type="label">
          #shadow-root (open)
            <label> "Sample Label"
            </label>
        </ub-text>
      </div>
      <div class="css=16v52f"> // <<< input
        <div class="css-4cf88t">
          <div class="css-16v3hw">
            <ub-edit-field editor="textbox" value="Sample Value">
              #shadow-root (open)
                <div class="ub-edit-field__container">
                  <input type="text">
                </div>
            </ub-edit-field>
          </div>
        </div>
      </div>
  </div>
</ub-form-group>
```
</details>
