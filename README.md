
<h2> Nested frames </h2>

<b> PROBLEM: </b>Application was creating a new frameset each time you navigated to a main page. Cypress does not support frames natively, a temporary workaround was needed until devs fixed the issue. 
<details>
  <summary>html</summary>
  
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
