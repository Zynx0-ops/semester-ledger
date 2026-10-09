# Cross-device sync

**What was built: the Claude `db` capability — a realtime document store attached
to the artifact.** Open the planner on your laptop and your phone while signed
into the same account and both stay in step, with no account to create, no keys
to paste, and nothing to pay for.

## Why this, and what else was considered

| Option | Cost | Setup | Offline | Realtime |
|---|---|---|---|---|
| **Artifact `db`** (built) | Free | **None** — identity is your existing sign-in | Reads work from the local mirror; writes queue and retry | Yes, `onSnapshot` push |
| Firebase Firestore | Free tier is ample | Create a project, paste config | Best in class — a real offline queue | Yes |
| Supabase | Free tier 500 MB | Create a project, paste anon key, write SQL + row-level security | Weak — no built-in offline queue | Yes, via websockets |
| Own hosted backend | Hosting + upkeep | High — API, auth, deploy | Whatever you build | Whatever you build |
| File sync (Drive/Dropbox) | Free | OAuth app registration | Good | No — file-level, and it conflict-copies | 

Firestore is the better engine in the abstract: its offline persistence is
genuinely excellent. It loses here on the thing that actually decides it — it
needs a Google Cloud project, a config blob committed to a public repo, and a
decision about auth, to achieve something your Claude sign-in already gives for
free. Supabase is the same trade with weaker offline support. A hosted backend is
not worth running for one person's task list. File sync resolves conflicts by
leaving you two copies of the file, which is the worst outcome of the five.

**The one real limitation:** `db` exists only in the Claude artifact build. The
GitHub Pages copy has no such API and stays local to its browser. If you want
sync on the public URL instead, Firestore is the pick and the keys would have to
live in the repo.

## How tasks are stored

One document per task and per event, keyed by its id:

```
tasks/<id>     one document per task
events/<id>    one document per event
meta/classes   { list: [...] }   one doc, because order matters
meta/settings  { v: {...} }
meta/purged    { uids: [...] }   ids cleared on purpose, so an import cannot resurrect them
```

Per-task documents are the important choice. It means two devices editing
**different** tasks never collide — each writes its own document and both
survive. Classes ride in a single document because their array order *is* the
display order, and merging two orderings is worse than picking one.

## How changes propagate

1. A change updates memory, writes `localStorage` immediately, and re-renders —
   so the UI never waits on the network.
2. A debounced push (~700 ms) **diffs** current state against the last state
   confirmed by the server and writes only what changed. Nothing is resent
   wholesale on every keystroke.
3. Every device holds an `onSnapshot` subscription per collection. The server
   pushes the change out, and the other device applies and re-renders.
4. Incoming documents are run through the same `normalize()` as saved data —
   shared state is untrusted input, even when the only writer is you.

**Offline:** the local mirror means the app works fully offline. A failed push
clears the "last confirmed" marker so the next attempt re-sends everything, and
retries on a timer, on `online`, and when the tab becomes visible. Deletes are
tracked separately and retried until the server confirms them, because a lost
delete is the one failure a user would actually notice.

## How conflicts are resolved

`db` is **last-writer-wins per document**, and the data layout decides what that
means in practice:

- **Different tasks, at the same time** → both survive. This is the common case
  and it is handled by construction.
- **The same task, at the same time** → the later write wins; the earlier edit to
  that one task is lost. Each task carries `updatedAt` recording which device
  last changed it.
- **Edited on one device, deleted on another** → the delete wins. It is not
  re-created by the edit.
- **Settings** are shared, so changing the theme on your laptop changes it on
  your phone. Last write wins.

There is no merge of two simultaneous edits to the same field; that needs a CRDT
(Yjs, Automerge) plus a relay server to host it, which is a large amount of
machinery for a single-user planner. If you ever collaborate on this with someone
else, that is the point to revisit it.

## Privacy

A `db`-declaring artifact is organization-internal and cannot be shared publicly.
This one is private to you. Anyone you later grant access to would read and write
the same task documents — there is no per-person separation in this design.
