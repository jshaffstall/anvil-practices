## Dev vs Prod

Note that all of what's below is for people on the Free or Personal plans.  For those on the Professional plan or above, use staging environments in the Beta editor instead.

Applications are generally under development even after they're released.  We need a place we can make changes without affecting the version the users are using.  Anvil has a soft version of this in that you can publish a particular version of an app.  Users who come in through the public URL will use the published version, while the IDE will use the most current version.  

But the published and current version share data tables and data, which is not always what you want.  This document describes my workflow for managing the dev and prod environments as separate Anvil apps that share a single codebase.

## Setup

I use the [Git Setup portion of the official Anvil](https://anvil.works/docs/how-to/development-production) page to set up the repositories on my machine.

## Deploying Code Changes From Dev To Prod

If there are no data table changes, you can use the General Workflow section from the above link to push code changes from dev to prod.  That page says you shouldn't have to use --force.  That's true only if you haven't made any changes in the IDE to prod.  If you have made changes in the IDE to prod, even something as simple as changing the project name, you'll have to use --force.

Do not have the Prod app open in the Anvil IDE when you are trying to push Dev changes!

## Deploying Code & Data Table Changes From Dev To Prod

When you have to modify the data table schema in dev, pushing changes to prod becomes more complicated.  You must:

1) Identify changes made to the dev schema that are not yet reflected in prod

2) Make the those schema changes in prod

3) Force push the code changes from dev to prod.  You must force this one because changing the schema in prod changes the anvil.yaml file, which causes a normal push to not work.

### Identify changes made to the dev schema that are not yet reflected in prod

James Alexander wrote a nifty utility that allows you to automatically detect these differences.  [See the post with his original code here](https://anvil.works/forum/t/dev-prod-workflow-data-tables/7400/14).  You may need to modify the original code slightly (for me, the logic was backwards, and I wanted the info printed out differently).  

To use that you must have a local git repository of your prod app.  The only purpose of that local version of prod is to get access to the prod anvil.yaml file.  To identify schema changes:

1) git pull in the dev repo
2) git pull in the prod repo
3) Run the utility you modified to suit your needs

## Emergency Changes To Prod

There are times when you simply must make a code change directly to prod to fix (or more likely temporarily workaround) a breaking issue that's affecting your users.  When you do that, you must close the loop by:

1) Fix the issue in dev, possibly with a better mechanism than your emergency fix
2) Force push the change to prod, so it is back in line with dev
