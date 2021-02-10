## Dev vs Prod

Applications are generally under development even after they're released.  We need a place we can make changes without affecting the version the users are using.  While Anvil has a soft version of this in that you can publish a particular version of an app.  Users who come in through the public URL will use the published version, while the IDE will use the most current version.  

But the published and current version share data tables and data, which is not always what you want.  This document describes my workflow for managing the dev and prod environments as separate Anvil apps that share a single codebase.

## Setup

I use the (Git Setup portion of the official Anvil)[https://anvil.works/docs/how-to/development-production] page to set up the repositories on my machine.
