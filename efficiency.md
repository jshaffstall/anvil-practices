## [Server Module Imports](https://anvil.works/forum/t/server-call-difference-between-two-anvil-apps-to-return-short-dict-is-2-seconds/5629/18)

Any server call has to import everything even if it isn't used in that particular server call.  To avoid this delay, we can import rarely used modules inside the server functions that actually use them.  We can also time how long imports take to know which ones take up the most time.  The post linked above has the details.

## Minimize Server Rountrips

Each server call takes time.  Rather than having a form make multiple server calls in a row, make a single server call that returns all the information the form needs.  

