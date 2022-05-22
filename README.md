<h1> cypress-solutions</h1>
<hr/></hr>
A collection of cypress functions for specific scenarios
<h2> Nested frames </h2>
<b> PROBLEM: </b>Application was creating a new frameset each time you navigated to a main page. Cypress does not support frames natively, a temporary workaround was needed until devs fixed the issue. 

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
<b> SOLUTION: </b>Recursive selector for variable number of nested frames. 

```typescript
function loopFrames(frame-id) {
  return cy.get(frame-id).then((frame) => {
    const rootdiv = frame.contents().find('[id="root"]'); // we check if root div
    const nestedframe = frame.contents().find(frame-id); // and another frame are present
    if (nestedframe.length > 0) loopFrames(nestedframe); // and recall the function if we find another frame
    else cy.wrap(rootdiv); // finally, we return the rootdiv under the last frame
    });
}
```
