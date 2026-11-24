---
title: How do I connect my app to a Pivobase backend
description: "The URL and the anon key of a Pivobase backend, how to give them to a supabase-js app, and the exact variable names a Lovable project reads. Includes the names used by TanStack Start server code and the reminder to rebuild after changing them."
last_verified: 2026-11-22
---

## Connecting an app to your backend

Your app needs two values from the backend's page in the console: the URL and
the anon key. Both are shown in full under `API keys`, each with a `Copy` button.
The `Connect your app` box under them already contains your real values, so you
can copy the code from there instead of typing anything.

The anon key is made to be used in browser code, as long as row security is on
for your tables. The service key is not. It bypasses row security, so it never
goes in browser code and it is never part of the `Connect your app` box. The page
on keys explains the difference.

### supabase-js

Install the client:

```bash
npm install @supabase/supabase-js
```

Then create it with your two values:

```js
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  '<your-backend-url>',
  '<your-anon-key>'
)
```

Use the URL exactly as the console shows it, with no path added at the end.

### Lovable

A Lovable project reads two variables when it builds. Put these two lines in
your project's `.env` file:

```text
VITE_SUPABASE_URL=<your-backend-url>
VITE_SUPABASE_PUBLISHABLE_KEY=<your-anon-key>
```

Some older projects read the anon key from a variable with the older name
`VITE_SUPABASE_ANON_KEY`. If your project already uses that name, keep it and put
the anon key there. A project that also wants the backend's reference reads it
from `VITE_SUPABASE_PROJECT_ID`. The reference is the short random code at the
start of your URL.

Server code that uses TanStack Start reads the same three values under names
without the `VITE_` prefix:

- `SUPABASE_URL`
- `SUPABASE_PUBLISHABLE_KEY`
- `SUPABASE_PROJECT_ID`

**Rebuild after changing them.** Vite reads these variables when it builds your
app, not when a visitor opens it. If you change a value and do not rebuild, the
running app keeps the old one.

### If the connection fails

- Check that the URL has no extra characters and starts with `https://`.
- Check that you pasted the anon key and not the service key.
- If you regenerated the keys, your app still has the old ones. Copy the new
  values from the console and rebuild.
