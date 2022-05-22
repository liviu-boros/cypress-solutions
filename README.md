<h1> cypress-solutions</h1>
<hr/></hr>
A collection of cypress functions for specific scenarios
<h2> Nested frames </h2>
<b> PROBLEM: </b> Application was creating a new frameset each time you navigated to a main page. Cypress does not support frames natively, a temporary workaround was needed until devs fixed the issue.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    
  </body>
</html>
```

<h4> Recursive selector for variable number of nested frames </h4>

```typescript
function loopFrames(frame-id) {
  return cy.get(frame-id).then((frame) => {
    // we check if root div and another frame are present
    const rootdiv = frame.contents().find('[id="root"]');
    const nestedframe = frame.contents().find(frame-id);
    // and recall the function if we find another frame
    if (nestedframe.length > 0) loopFrames(nestedframe);
    // we return the rootdiv under the last frame
    else cy.wrap(rootdiv);
    });
}
```
