<h2>
	<img src="https://img.icons8.com/wired/42/04C38E/activity-feed.png" width="42" height="42">
	&nbsp; Nested frames &nbsp;
</h2>
      
<details>
  <summary>
  </summary>
	
<b> PROBLEM: </b>Application was creating a new frameset each time you navigated to a main page. Cypress does not support frames natively, a temporary workaround was needed until devs fixed the issue.
	
	
<b> SOLUTION: </b>Recursive selector for variable number of nested frames. 
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
