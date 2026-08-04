# Changelog

## 0.9.15

**Fixes a second machine that can never push, and never stops saying "not
synced". Upgrade every machine.**

- **A machine could be left unable to push at all.** A pull rewrites every path
  inside a transcript to the local checkout, so two machines holding the same
  conversation legitimately hold different numbers of bytes. Both the panel tag
  and the push guard compared our byte count against the *pushing* machine's,
  recorded on Drive. On a machine whose paths are shorter, every session read
  as smaller forever: the tag said "not synced" however often sync was pressed,
  and the guard read the same difference as "Drive is longer, do not truncate
  it" and deferred the upload. Every session, every cycle.
- Both now compare against a **local watermark** — what this machine's own file
  looked like when it last pushed or pulled — and the push guard asks the only
  question that means anything across machines: *has the Drive copy moved since
  we last agreed?*
- **A 70 MB transcript is no longer re-downloaded and re-rewritten every
  cycle.** The pull skipped only when the byte counts matched, which on a
  second machine they never did.
- **Forked sessions are tagged `forked`, in red, instead of `not synced`.**
  Sync cannot resolve a fork, so telling you to press sync was an instruction
  that could never work. The tooltip says what actually resolves it.

## 0.9.14

**The not-synced tag no longer cries wolf. Reported within the hour by its
first user â€” twice, correctly.**

- **Opening a session just to read it flipped the tag.** Claude Code touches
  the transcript when a session is opened: the mtime moves, the size does not.
  Verified against a real file â€” modified time newer, grown by exactly zero
  bytes. A touch is not a change; same size now reads as in sync.
- **Closing a session after syncing flipped it too**, which read as "you must
  close before you sync". Closing writes derived records â€” the summary, the
  title. The tag now reads only the bytes appended since the last push and asks
  whether any of them is a conversation turn (a record with a `uuid`; summaries
  carry `leafUuid`, which deliberately does not match). Titles and summaries
  travel on the next sync as they always did â€” quietly.
- Reading the appended bytes costs almost nothing: on a 70 MB transcript whose
  owner pressed close, the tail is a few hundred bytes, and the answer is
  cached until the file changes again.
- A transcript that *shrank* against Drive's copy is tagged rather than
  explained away â€” that is not what an append looks like.

## 0.9.13

- **The `not synced` tag now works from the moment 0.9.12 is installed**, not
  from its second sync. The exact comparison needs the per-session metadata
  that only a 0.9.12+ sync stores, and state written by older builds has none
  of it â€” so the one session the tag was built for kept reading `synced` until
  a sync happened to run. Until the metadata exists, the time of the last
  completed sync stands in: a transcript modified after it is not on Drive
  yet. With nothing known at all the panel says nothing rather than guesses.
- Excluded sessions are never tagged. Drive being behind is exactly what
  unticking asked for.

## 0.9.12

- **A session typed in after its last sync is tagged `not synced`**, in amber,
  where `synced` used to keep saying otherwise. That tag is the answer to "did
  I remember to press âŸ³ before leaving?" â€” the one question the panel could
  not answer, since `synced` only ever meant "a copy exists on Drive", not
  "the copy is current".
- The comparison costs nothing at sync time and nothing at panel time: every
  upload already records the transcript's size and mtime on the Drive file,
  and every pull stamps the local file with the same mtime â€” so on a synced
  session the two clocks are equal, and a local file strictly past Drive's
  has turns Drive lacks. The panel just compares the two numbers it already
  had.
- A push now updates the in-memory Drive listing it just wrote to, so a
  session uploaded seconds ago reads as synced immediately rather than
  after the next full cycle.

## 0.9.11

- **The panel now tells you when another machine has pushed something you have
  not pulled**: a banner â€” *2 sessions from laptop waiting on Drive â€” pull
  now* â€” and a â¬‡ count in the status bar. Until now the only way to know
  whether machine A's sync had arrived was to pull and see.
- Noticing costs one small request: Drive is asked what changed since this
  machine last looked. The decision uses Drive's own modified times as the
  watermark and the pushing machine's name recorded on every upload â€” so your
  own pushes are never echoed back, a skewed local clock cannot hide anything,
  and no hashes or downloads are involved in noticing. Whether a pull actually
  replaces a file is still decided the usual way: size and mtime for
  transcripts, md5 for the small files, record uuids on any dispute.
- Checked when the panel opens and every few minutes while it stays open â€”
  never in the background with the panel closed, never twice within a minute.
  `claudeStateSync.remoteCheck.minutes` sets the pace; 0 turns it off.
- The news persists until a sync pulls it, so noticing is not forgetting:
  re-checks cannot lose what was already announced.

## 0.9.10

**Fixes a machine that syncs and still never sees the other side's latest turns.
Upgrade both machines.**

- **A stale path rewrite could freeze a session on one machine, permanently.**
  A pulled transcript is rewritten to the local checkout by whichever build
  pulled it. If the rewrite rules have changed since â€” the drive-letter case
  fix, a moved project â€” the next pull differs from the local copy in bytes
  while being the same conversation, and the byte comparison called that a
  fork: the download was parked aside, the visible file left old. Every later
  sync repeated it. Forks are now confirmed at the record level â€” line count
  and the per-record uuids, which no rewrite touches â€” and only a fork the
  records also show is treated as one.
- **A session open on the receiving machine is no longer replaced underneath
  the window.** The running conversation keeps its state in that process, so
  the file swap changed nothing on screen while the process kept appending to
  the swapped file â€” "sync did nothing", with two histories interleaving on
  disk underneath. The pull now leaves a live session alone and says so in the
  log; close it, sync, resume.
- If machine B pressed sync while machine A was still uploading, B pulled
  whatever had reached Drive at that moment. The README now says to let A
  finish â€” the progress numbers from 0.9.4 show when that is.

## 0.9.9

- **The setup screens now carry the whole panel underneath them**, not a summary
  of it: your projects, every session by name and size, the shared scopes, the
  activity list. All of it is read from disk, so it is real and populated before
  Google is involved at all â€” and it answers "what am I setting this up for"
  better than any description above it does.
- Sync appears on those screens too, disabled and saying why. A button that
  materialises only once everything works gives no hint that it is the point of
  the work.
- Sign out and switch account stay hidden until there is an account to act on,
  and the Drive row says *not connected yet* rather than *signed in*.
- **Fixed: a project card said "sessions none yet" directly above its sessions.**
  The count came from the sync stats, which a sync writes â€” so before the first
  one it was 0 while the transcripts sat on disk in plain sight. It counts the
  rows now.
- The build pins what each screen must and must not contain, and the panel
  fixture uses the field names the panel actually reads. It had invented its
  own, so it was checking nothing about how a session draws.

## 0.9.8

- **The screen you actually land on now shows your projects and sessions too.**
  0.9.7 put them on the first-run and sign-in screens but not on the one for a
  sign-in whose OAuth client has gone â€” which is the screen an existing install
  lands on after an update, so in practice nobody saw them.
- The build now pins what each screen must say, not only that it draws without
  throwing. A setup screen missing the list looks perfectly healthy to a
  renderer; it is a regression to a reader.

## 0.9.7

- **A first run now opens on what the extension does**, not on four errands in
  Google Cloud Console. Four lines â€” state on every machine, carrying a
  conversation across, your own Drive, nothing without asking â€” then the setup.
- **And on your own projects and sessions, by name.** All of that is read from
  disk and needs no Google account, so the panel can show what would be carried
  across before you have connected anything: the projects it found, the
  conversations in them and what they weigh.
- The sign-in step shows the same, so the last screen before connecting says
  what connecting is for.
- **The build now draws the panel rather than only parsing it.** A missing
  function or a property read off nothing throws when the panel renders, which
  ships as a blank sidebar with a clean build â€” that has happened here once
  already. `check-webview.mjs` runs the panel in every state it has, including a
  machine with no projects, and fails on a throw, on nothing drawn, and on
  `undefined` or `NaN` reaching the markup.

## 0.9.6

- **The client is entered in the panel now**, in two boxes with a Save button,
  instead of two prompts that appear one after the other at the top of the
  window. Enter saves from either box â€” in the prompts it silently did nothing
  when the ID had not been recognised, which reads exactly like a key that does
  not work.
- **Paste the whole `client_secretâ€¦.json`** Google gives you into either box and
  both fill in. It is the file you downloaded; picking two fields out of it by
  eye was work with no purpose. The command in the palette takes it too, and
  stops asking for the secret when the JSON already carried one.
- Why the prompts were the wrong shape: the second one asked for the secret only
  after the first was accepted, so anyone who did not have it to hand pressed
  Escape and nothing was saved â€” with no sign that nothing had been. Both boxes
  are visible at once and say what is wrong, next to the box that is wrong.
- **Typing into the panel is no longer interrupted by the panel.** It repaints on
  its own â€” a session opening is enough â€” and that used to wipe a half-typed
  value and take the caret with it.

## 0.9.5

**Fixes a panel that looks signed in while every sync fails. Upgrade.**

- **"Not signed in to Google Drive." while plainly signed in.** Reaching Drive
  needs two things kept in different places: the refresh token, which lives in
  secret storage and outlives any build, and the OAuth client, which is either
  pasted in or baked into the build. Replace a build that carried a client with
  one that does not and the token survives with nothing to redeem it against.
  The panel asked only about the token, so it drew the full signed-in view and
  Sync now failed on every press.
- The panel now says what actually happened, and offers the one thing that
  fixes it: paste the same client back in. **No new sign-in is needed** â€” a
  refresh token belongs to the client that issued it, so the same client picks
  the session up where it left off. The sign-in button, which is what the old
  message sent you to, could never have fixed this.
- The two failures are told apart everywhere â€” panel, Sync now and the error
  itself. `authReadiness()` decides it in one place, and a test pins all four
  combinations.

## 0.9.4

- **A sync now says how far along it is** â€” the percentage and the file count
  beside the name of the file being worked on. The fraction was already being
  computed; it only ever set the width of a 2px bar, which on a long sync is
  indistinguishable from a spinner.
- **Fixed: the bar never showed the indeterminate phase.** It is drawn that way
  while Drive is being listed, because the total cannot be counted before then â€”
  but the moving segment was also given an inline `width:0%`, and an inline
  style outranks the stylesheet, so what should have been a sliding bar was an
  empty track.
- **Fixed: the bar kept sliding after the total was known.** The animation was
  never taken off once there was a real fraction, so the fill was positioned and
  animated at once, travelling across the bar rather than filling it.
- The bar is 3px rather than 2px, and the count uses tabular figures so it does
  not jitter as the digits change.

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
- Cross-platform audit otherwise clean â€” `os.homedir()` and `os.tmpdir()` for
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
- This reads local files only. Nothing here syncs â€” that is still yours to ask
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
  parentage â€” the Claude extension spawns its CLI from the same extension host
  this code runs in â€” and where a window holds several, the most recently
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
  fixed position â€” one real transcript carries its summary at line 4, another at
  line 267 of 269, a third a quarter of the way in â€” and only the head was being
  read, so sessions with a later title fell back to their first prompt. Both ends
  are read now, the newest record wins, and a file small enough to read whole is
  read whole rather than showing a name that has since been replaced.

## 0.7.3

- Hand-drawn Marketplace icon: two machines either side of the sync arrows.
- The activity bar glyph matches it â€” the same sync arrows, now with a burst
  inside the ring.
- `npm run icon` no longer overwrites the icon; it needs `--force`.

## 0.7.2

**Fixes a panel stuck on "Loadingâ€¦" in 0.7.1. Upgrade.**

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
  do in Google Cloud Console, each with a button that opens the right page â€”
  including the one that is easy to miss: publishing the consent screen, without
  which Google expires the sign-in every seven days.
- The sign-in step says what to do about the "unverified app" warning, since
  every self-registered client shows it.

## 0.6.1

- Ready to publish: a 128Ã—128 icon, and a `vscode:prepublish` step so
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
  exactly as given â€” `C:\Users\me\app` becomes `C--Users-me-app`, not
  `c--Users-me-app`. Lower-casing it here risked creating a second folder
  alongside the real one on a machine that had none yet.

## 0.5.1

- **Progress bar while syncing**, with the file being worked on underneath. It
  starts indeterminate while Drive is being listed â€” the total is not knowable
  before that â€” then becomes a real fraction over every file and transcript.
- The sync icon no longer dims while it spins; the spin alone says it is busy.
- **"Sync othersâ€¦" removed.**
- The global section is now **"Shared from ~/.claude"**, with the scopes as
  chips and each figure on its own line instead of one crowded row.

## 0.5.0

- **Automatic syncing is gone.** No polling timer, no file watcher, no sync on
  startup. A cycle runs when you press âŸ³, run **Sync Now**, sign in, adopt a
  project, or switch a session back on. The `enabled`, `pullIntervalSec` and
  `pushDebounceMs` settings are removed, as is the pause button.
- **The version is shown in the panel header**, so it is obvious which build is
  actually running after an install.
- **Fixed: the session checkboxes did nothing.** A stray NUL byte had replaced
  the space in the separator used to parse `"<project> <session>"`, so every
  toggle was discarded before it reached the state file. Ticking a session held
  only on Drive now brings it back â€” and, since nothing runs in the background
  any more, doing so starts a sync immediately.

## 0.4.2

- Deleting a session updates the panel immediately. The row used to linger as
  "on Drive" until the next cycle refreshed the cached listing, which made a
  successful delete look like it had not worked and left you clicking sync.
- Each delete now says exactly what it did â€” and, for *Delete everywhere*,
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
  sessions â€” labelled with the conversation's summary or first prompt rather
  than a bare UUID â€” with size and whether the copy is synced, local only, or
  waiting on Drive. Unticking one stops it being uploaded *and* downloaded.
- Sessions held only on Drive appear too, so a large transcript can be switched
  off before it is ever pulled to this machine.
- Choices are remembered per project and survive reloads.

## 0.3.4

**Fixes transcript corruption. Upgrade before syncing again.**

- Downloaded transcripts were decoded chunk by chunk, so any character whose
  UTF-8 bytes straddled a chunk boundary â€” Thai, CJK, emoji â€” was replaced with
  `U+FFFD` and lost. Roughly one character per 32 KB of text. Decoding now
  carries incomplete byte sequences across boundaries.
- That corruption also caused **false forks**: a downloaded copy no longer
  matched the local file byte for byte, so identical histories were reported as
  having diverged and parked under `forks/`. Those parked copies are damaged
  duplicates and can be deleted.

## 0.3.3

- **A network blip no longer signs you out.** Any failure while refreshing the
  access token was treated as a revoked grant, so a dropped connection deleted
  the refresh token and demanded a fresh sign-in â€” reported, confusingly, as
  "sign-in expired (fetch failed)". Only an actual rejection from Google now
  clears the sign-in; unreachable network, proxy replies and 5xx are retried.
- Being offline shows as `$(cloud-offline)` in the status bar and one "offline â€”
  will retry" line in the activity feed, rather than an error every poll.

## 0.3.2

- **An explicit sync now uploads the session you were just in.** The idle wait
  that stops a live conversation from re-uploading every tick used to apply to
  manual syncs too, so clicking sync before shutting down left the last turns
  behind â€” exactly the moment they matter. Background syncs still wait.

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
  re-checked: the older entry wins â€” decided identically on every machine â€” and
  the loser's empty duplicate is removed.
- **Polling is jittered** so machines left running side by side drift apart
  instead of colliding on every tick.

## 0.2.1

- The panel now says when each scope was last written on Drive **and which
  machine wrote it** â€” "4m ago from OFFICE-PC", or "this machine" when it was
  this one. Every uploaded file is stamped with its machine name.
- A header line shows the last completed sync cycle and this machine's name.
- The inline webview script is syntax-checked during build, so a typo there
  fails the build instead of rendering an empty sidebar.

## 0.2.0

- New sidebar panel: account, per-project rows with file counts and last
  up/down times, global scope summary, conflicts, and an activity feed with an
  Issues filter. Every action is a single click â€” sync, pause, switch account,
  resolve a conflict, open the log.
- **Sync othersâ€¦** adopts a project that exists on Drive but is not open in this
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
- Project path encoding that matches Claude Code exactly â€” every non-alphanumeric
  character becomes `-`, so Windows drive letters, spaces and underscores all land in
  the folder Claude Code actually uses.
- Paths inside transcripts rewritten on download, streamed so large files never sit in
  memory whole.
- Three-way merge via `git merge-file`; conflicts leave the local file untouched and
  surface in the sidebar with a local â†” remote diff.
- `settings.json` (opt-in) stored with `${HOME}` templating so hook and MCP paths
  survive moving between machines and operating systems.
- Background sync: poll on an interval, push shortly after a local write.
