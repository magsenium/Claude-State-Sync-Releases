# Changelog

## 0.9.3

- The description now says that sessions can be copied between projects, which
  the listing never mentioned.

## 0.9.2

- The copy and delete buttons on each session are always visible rather than
  appearing on hover. A control nobody knows is there may as well not exist, and
  hover-only ones are awkward on a touchpad.

## 0.9.1

- **Case comparisons now follow the filesystem.** Folder matching and path
  rewriting ignored case everywhere, which is right on Windows and macOS but
  wrong on Linux, where `/home/me/App` and `/home/me/app` are different
  projects: a session could have bound to the wrong folder, or a rewrite could
  have altered a path it should not have touched. Both branches are tested.
- Cross-platform audit otherwise clean — `os.homedir()` and `os.tmpdir()` for
  locations, `path.join` throughout, the only external program is `git`, and the
  session watcher is non-recursive, which Linux requires.

## 0.9.0

- **Copy a session into another project.** Each row gains a copy button next to
  the delete one: pick a project Claude Code already knows, or any folder, and
  the transcript is filed there with every path inside rewritten. Filing is by
  encoded directory *and* by paths written throughout the file, so this is a
  rewrite rather than a rename.
- **It copies rather than moves, deliberately.** The rewrite touches every line
  of a file that is the only record of a conversation, and this extension has
  already shipped one rewriting bug that damaged transcripts. The original is
  left exactly where it was; the row's delete button is there once you have
  looked at the copy.
- **The result is checked before it is kept**: same number of records, every one
  still parsable, no damaged characters. A copy that fails is discarded and the
  reason reported.
- The button is disabled while a session is running, since Claude keeps
  appending and the copy would be a snapshot that falls behind.
- `scripts/copy-session.mjs` does the same from a terminal, for projects that
  are not open.

## 0.8.2

- The panel keeps itself up to date. Starting or closing a session used to
  require switching to another view and back before `live` caught up; the
  session registry is now watched, so a session appearing or ending updates the
  list within a moment. A slow tick while the panel is on screen catches what no
  event announces, such as a registry file orphaned by a crash.
- This reads local files only. Nothing here syncs — that is still yours to ask
  for.

## 0.8.1

- The **current** tag is withdrawn. Identifying the session in this window by
  process parentage was sound, but picking one when a window holds several
  relied on guessing from write times, and in practice it labelled the wrong
  session. A tag that is right most of the time is worse than no tag. **live**
  stays, since it is read directly rather than inferred.

## 0.8.0

- The session open in this window is tagged **current**, alongside **live** for
  any session with a Claude Code process attached. Ownership comes from
  parentage — the Claude extension spawns its CLI from the same extension host
  this code runs in — and where a window holds several, the most recently
  written transcript is taken as the one being typed in.
- The lookup does not delay the panel: the first one costs about a second on
  Windows, so it runs in the background and the panel refreshes if the answer
  changes. Afterwards it is cached against the set of running processes.

## 0.7.5

- A session with a Claude Code process attached is marked **live**. Its name
  will not match the one Claude shows, and now says why: while a session is
  open the title lives in that process and is only written into the transcript
  when it ends, so there is nothing on disk to read until then.

## 0.7.4

- **Session names now match what Claude Code shows.** The title record has no
  fixed position — one real transcript carries its summary at line 4, another at
  line 267 of 269, a third a quarter of the way in — and only the head was being
  read, so sessions with a later title fell back to their first prompt. Both ends
  are read now, the newest record wins, and a file small enough to read whole is
  read whole rather than showing a name that has since been replaced.

## 0.7.3

- Hand-drawn Marketplace icon: two machines either side of the sync arrows.
- The activity bar glyph matches it — the same sync arrows, now with a burst
  inside the ring.
- `npm run icon` no longer overwrites the icon; it needs `--force`.

## 0.7.2

**Fixes a panel stuck on "Loading…" in 0.7.1. Upgrade.**

- An apostrophe written as `\'` inside the panel's inline script was consumed by
  the surrounding template literal, leaving an unterminated string. The whole
  script failed to parse, so nothing ever replaced the placeholder. The build
  check now parses the script *as emitted* rather than as written, and catches
  exactly this.
- If the panel cannot build its status for any other reason it now says so, with
  a button to open the log, instead of sitting on the placeholder.
- New icon: Claude's orange with the sync arrows, matching the activity bar.
- Test fixtures and doc comments use invented project paths. They had been
  copied from a real machine and carried real project and folder names.

## 0.7.1

- The setup panel now explains each step rather than naming it: why a
  registration is needed at all, that "External" does not mean public, that
  "Internal" being greyed out is normal, and what leaving the consent screen on
  "Testing" actually costs you.
- README rewritten: shorter everywhere except the OAuth walkthrough, which is
  the only part that takes real effort.

## 0.7.0

- **Setup is now explained in the panel.** A first run lists the four things to
  do in Google Cloud Console, each with a button that opens the right page —
  including the one that is easy to miss: publishing the consent screen, without
  which Google expires the sign-in every seven days.
- The sign-in step says what to do about the "unverified app" warning, since
  every self-registered client shows it.

## 0.6.1

- Ready to publish: a 128×128 icon, and a `vscode:prepublish` step so
  `vsce publish` builds through the same checks as everything else.
- **Embedding an OAuth client is now opt-in** (`CLAUDE_SYNC_EMBED=1` alongside
  the credentials). `vsce publish` runs the same build, and a shell that merely
  had the credentials exported would otherwise have shipped a personal client to
  every installer. The build says which kind it produced.

## 0.6.0

- **Renamed sessions show their name.** `/rename`, `claude -n` and Ctrl+R in the
  picker write a `custom-title` record into the transcript itself, so the name
  travels with the file. The panel now prefers it over the derived summary and
  the first prompt, reading the end of the file so a rename made hours into a
  70 MB conversation is still found, and the most recent rename wins.
- **Fixed: drive-letter case.** Claude Code encodes the working directory
  exactly as given — `C:\Users\me\app` becomes `C--Users-me-app`, not
  `c--Users-me-app`. Lower-casing it here risked creating a second folder
  alongside the real one on a machine that had none yet.

## 0.5.1

- **Progress bar while syncing**, with the file being worked on underneath. It
  starts indeterminate while Drive is being listed — the total is not knowable
  before that — then becomes a real fraction over every file and transcript.
- The sync icon no longer dims while it spins; the spin alone says it is busy.
- **"Sync others…" removed.**
- The global section is now **"Shared from ~/.claude"**, with the scopes as
  chips and each figure on its own line instead of one crowded row.

## 0.5.0

- **Automatic syncing is gone.** No polling timer, no file watcher, no sync on
  startup. A cycle runs when you press ⟳, run **Sync Now**, sign in, adopt a
  project, or switch a session back on. The `enabled`, `pullIntervalSec` and
  `pushDebounceMs` settings are removed, as is the pause button.
- **The version is shown in the panel header**, so it is obvious which build is
  actually running after an install.
- **Fixed: the session checkboxes did nothing.** A stray NUL byte had replaced
  the space in the separator used to parse `"<project> <session>"`, so every
  toggle was discarded before it reached the state file. Ticking a session held
  only on Drive now brings it back — and, since nothing runs in the background
  any more, doing so starts a sync immediately.

## 0.4.2

- Deleting a session updates the panel immediately. The row used to linger as
  "on Drive" until the next cycle refreshed the cached listing, which made a
  successful delete look like it had not worked and left you clicking sync.
- Each delete now says exactly what it did — and, for *Delete everywhere*,
  that another machine still holding the transcript will upload it again unless
  it is deleted there too.

## 0.4.1

- The session list is always visible; the show/hide toggle is gone.
- **Delete a session** from its row. The confirmation offers *Delete here*
  (this machine, and it stops syncing back) or *Delete everywhere* (also the
  copy on Drive). Deleting always excludes the session, since otherwise the next
  cycle would download it again.
- **Discard** parked fork copies straight from the panel.

## 0.4.0

- **Per-session list with checkboxes.** Each project expands to show its
  sessions — labelled with the conversation's summary or first prompt rather
  than a bare UUID — with size and whether the copy is synced, local only, or
  waiting on Drive. Unticking one stops it being uploaded *and* downloaded.
- Sessions held only on Drive appear too, so a large transcript can be switched
  off before it is ever pulled to this machine.
- Choices are remembered per project and survive reloads.

## 0.3.4

**Fixes transcript corruption. Upgrade before syncing again.**

- Downloaded transcripts were decoded chunk by chunk, so any character whose
  UTF-8 bytes straddled a chunk boundary — Thai, CJK, emoji — was replaced with
  `U+FFFD` and lost. Roughly one character per 32 KB of text. Decoding now
  carries incomplete byte sequences across boundaries.
- That corruption also caused **false forks**: a downloaded copy no longer
  matched the local file byte for byte, so identical histories were reported as
  having diverged and parked under `forks/`. Those parked copies are damaged
  duplicates and can be deleted.

## 0.3.3

- **A network blip no longer signs you out.** Any failure while refreshing the
  access token was treated as a revoked grant, so a dropped connection deleted
  the refresh token and demanded a fresh sign-in — reported, confusingly, as
  "sign-in expired (fetch failed)". Only an actual rejection from Google now
  clears the sign-in; unreachable network, proxy replies and 5xx are retried.
- Being offline shows as `$(cloud-offline)` in the status bar and one "offline —
  will retry" line in the activity feed, rather than an error every poll.

## 0.3.2

- **An explicit sync now uploads the session you were just in.** The idle wait
  that stops a live conversation from re-uploading every tick used to apply to
  manual syncs too, so clicking sync before shutting down left the last turns
  behind — exactly the moment they matter. Background syncs still wait.

## 0.3.1

- **A session transcript is never overwritten with divergent content.** Copies
  are compared by content prefix: if one side is simply further along it wins,
  but if the same session was continued on both machines the incoming copy is
  parked under `~/.claude-state-sync/forks/` and both histories survive.
- Parked copies live **outside** `~/.claude/projects`, so a fork never turns into
  a second entry in `claude --resume`. The panel says how many are held and
  opens the folder; parked copies are never uploaded.
- Pushes defer when the copy on Drive is longer than the local one, instead of
  truncating turns recorded elsewhere.

## 0.3.0

Hardening for two machines syncing at the same moment.

- **Lost updates fixed.** A push now re-checks the file on Drive immediately
  before writing. If another machine changed it since this cycle's index was
  read, the write is abandoned and reported as *deferred*; the next cycle sees
  both sides changed and merges properly instead of overwriting.
- **Duplicate Drive folders and files fixed.** Drive allows two entries with the
  same name in one parent, so simultaneous creates used to leave two folders and
  the machines would sync into different ones forever. Creates are now
  re-checked: the older entry wins — decided identically on every machine — and
  the loser's empty duplicate is removed.
- **Polling is jittered** so machines left running side by side drift apart
  instead of colliding on every tick.

## 0.2.1

- The panel now says when each scope was last written on Drive **and which
  machine wrote it** — "4m ago from OFFICE-PC", or "this machine" when it was
  this one. Every uploaded file is stamped with its machine name.
- A header line shows the last completed sync cycle and this machine's name.
- The inline webview script is syntax-checked during build, so a typo there
  fails the build instead of rendering an empty sidebar.

## 0.2.0

- New sidebar panel: account, per-project rows with file counts and last
  up/down times, global scope summary, conflicts, and an activity feed with an
  Issues filter. Every action is a single click — sync, pause, switch account,
  resolve a conflict, open the log.
- **Sync others…** adopts a project that exists on Drive but is not open in this
  window: pick it, point at the local checkout, and it keeps syncing from then on.
- Per-scope counters and timestamps persist across reloads.

## 0.1.1

- An OAuth client can be baked in at build time (`CLAUDE_SYNC_CLIENT_ID` /
  `CLAUDE_SYNC_CLIENT_SECRET`), so a fresh machine needs only the sign-in click.
  `claudeStateSync.oauth.useBuiltin` turns it off in favour of a per-machine client.
- Refresh failures now name the usual cause: an OAuth consent screen left in
  "Testing", which Google expires after 7 days.

## 0.1.0

First release.

- Sync `CLAUDE.md`, `plans/`, `skills/`, `agents/`, `commands/` and per-project
  `memory/` through your own Google Drive, using the `drive.file` scope.
- Session transcripts backed up per project once a session has gone idle, gzipped,
  with modification times preserved on download.
- Project path encoding that matches Claude Code exactly — every non-alphanumeric
  character becomes `-`, so Windows drive letters, spaces and underscores all land in
  the folder Claude Code actually uses.
- Paths inside transcripts rewritten on download, streamed so large files never sit in
  memory whole.
- Three-way merge via `git merge-file`; conflicts leave the local file untouched and
  surface in the sidebar with a local ↔ remote diff.
- `settings.json` (opt-in) stored with `${HOME}` templating so hook and MCP paths
  survive moving between machines and operating systems.
- Background sync: poll on an interval, push shortly after a local write.
