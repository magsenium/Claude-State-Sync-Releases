# Changelog

## 0.10.19

- **A dividing rule between this window’s projects and the rest** of the
  Projects list — the two groups were hard to tell apart at a glance. Drawn
  only when both groups exist.

## 0.10.18

- **A project synced through another window now renders like its own window
  would.** Its folder is located by git remote and the card reads the local
  transcripts — real titles, sizes and true `synced`/`not synced` tags —
  instead of listing everything as `on Drive` with bare ids. Only a project
  genuinely absent from this machine falls back to the listing metadata.
- **The project-row pills are gone.** `synced only`, `on Drive`, `not
  syncing`, `via origin` and `by name` cluttered every row; what they said is
  visible on the session rows. A row now carries at most the green `this
  project` badge or the `start syncing` action.

## 0.10.17

- **An unsynced project’s card now lists its sessions, each tagged `on
  Drive`,** instead of a bare project-level pill: id, size, upload time and —
  for sessions pushed from 0.10.17 on — the session’s title, which now rides
  in the upload metadata (clipped to fit the store’s limits). Drawn entirely
  from the listing; no transcript is downloaded. Sessions already on Drive
  show their title after the machine that holds them syncs once more.

## 0.10.16

- **Clearer words on the project rows.** The green badge on the project open
  in this window now reads `this project` instead of `open here`, and the
  action on an `on Drive` row reads `start syncing` instead of `sync here` —
  each project syncs into its own folder on this machine; the list shows them
  all in one place purely for convenience, and the old wording suggested the
  files would land in the open project.

## 0.10.15

- **The per-project checkbox is gone again** — a project on this machine
  always syncs; choosing what moves is the job of the checkboxes on the
  session rows beneath it. What remains at project level: `sync here` on an
  `on Drive` row to start syncing a project that is not on this machine yet,
  and `stop syncing this one` riding next to the message on a link that
  cannot sync — the row survives either way.

## 0.10.14

- **Whether a project syncs here is now a checkbox on its row, and stopping is
  reversible.** `stop syncing this one` removed the card — and with it the
  only way to start again. Every project the store holds is now listed: open
  ones (box ticked and locked), synced ones (untick to stop — nothing is
  deleted, the row stays), and `on Drive` ones with an empty box that starts
  the sync when ticked, locating the folder by git remote and refusing a
  mismatched pick. The store list is refreshed on every sync, so a project
  unticked by accident is back one tick later.

## 0.10.13

- **`show all` / `fold others` on the Projects header** — one click expands
  every folded card to manage everything from this page, one click folds all
  but the open ones back down.
- **Copying a session now asks what to do with the paths inside it**, with the
  consequence written on each choice: rewrite them so Claude treats the copy
  as native to the new project (recommended), or keep a byte-for-byte copy
  whose paths still point at the original folder — an archive, not a
  continuation.
- **The done message says to reload the window that has the target project
  open** (with a reload button for this one): the Claude extension reads its
  session list on startup, so until a reload the copy looks like it failed.

## 0.10.12

- **A link that cannot sync now says so.** A project adopted with the wrong
  folder — or whose folder has since disappeared — used to either vanish from
  the panel or sit there looking healthy while syncing nowhere. Its card now
  reads `not syncing` with the reason spelled out ("no folder on this machine
  matches this project" / "the linked folder is gone") and offers the unlink,
  the engine skips it, and picking a folder that is really a different project
  is refused at link time instead of being stored.

## 0.10.11

- **The open-here badge and the folding keyed on the wrong flag.** 0.10.10
  treated `linked` (adopted from Drive) as "not open here" — but a project
  pulled here and then opened is both, so the very project being worked in
  could show `synced only`, folded, while everything else stood expanded.
  Open-ness now comes from the window's workspace folders: the open project
  is the one expanded and marked, and every other project folds to one line.

## 0.10.10

- **A session copied to a second project now reaches the other machine while
  the original is still open.** The do-not-touch-a-live-session guard matched
  by session id across the whole machine, and a copy keeps its id — so as long
  as the original conversation was open anywhere, the copy in the new project
  was silently skipped on every sync, though writing it would have touched
  nothing the running window holds. The guard now fires only when the file it
  would actually replace exists.
- **The Projects list says which project is open in this window** — a green
  `open here` on the one Claude can start sessions in, `synced only` on the
  adopted ones — and the adopted ones fold to a single line so they do not
  bury it. The caret unfolds any of them; open projects lead the list.

## 0.10.9

- **A session that cannot be pulled now says so.** Session-level failures were
  reported without naming their project, and the panel attaches an error to a
  project card by looking for the key in the text — so a transcript that
  refused to come down left its row reading `on Drive` for ever with nothing
  anywhere to explain it.
- **A missing source path no longer refuses the download.** Uploads record
  where the pushing machine kept the project, and the pull would not proceed
  without it. But a transcript states its own working directory on every line,
  so it is read from the file instead; and if even that is absent the
  conversation is taken unrewritten rather than abandoned, because the paths it
  carries are the only ones it ever had.

## 0.10.8

- **"Pull it here" finds the project itself.** It opened a folder browser
  pointing at whatever was already open, which is the one folder it certainly
  was not looking for. A project key *is* its normalised git remote, so a
  folder on this machine whose remote matches is not a guess but the same
  repository — and Claude Code records the path of every project it has been
  run in, which makes those folders findable without asking.
- Asking is now the fallback, and when it does ask it starts **beside** what is
  open rather than inside it: a sibling checkout is the common case.
- Found while testing it against the real machine rather than trusting it:
  the first version compared the raw remote (`github.com/you/app`) against the
  key (`github.com_you_app`), which could never match, so every adoption would
  have fallen through to the browser. Both sides go through the same sanitiser
  now, and a test pins that they differ.

## 0.10.7

- **A project this window has not got open can now be pulled anyway.** The
  banner used to say *open that project to sync it*, which is an instruction
  rather than a button. It offers **pull it here**: pick where that project
  lives on this machine, and it syncs from then on, with every sync, until you
  press *stop syncing this one*.
- That fills a hole that was there from the start — the bookkeeping could
  *stop* syncing a project it did not have open, but nothing could ever start
  one: `linkProject` existed and no code path called it.
- Adopting asks for the folder rather than guessing it. Every path recorded
  inside a transcript is rewritten to that folder on the way down, so a guess
  would rewrite a conversation to point somewhere it does not live.

## 0.10.6

- **Resolving a conflict is now the same one-click choice as settling a fork.**
  Pressing *resolve* opened the diff and then put the options in a corner
  notification, which slides away on its own — so what was left on screen was
  two files and no visible way to act on them.
- The choice is a dialog now, and **comparing is one of its answers** rather
  than something that happens first: *Compare them* opens the diff and brings
  the same question back, so looking never costs you the controls. It names the
  file, both sizes and which cloud the other copy came from, and says plainly
  that your local file is untouched until you pick.
- *Edit a merged copy* appears only when there is a merged file to edit — with
  no common ancestor, or on binary content, no merge was attempted and offering
  one was a dead end.
- The conflict row carries the same red control a forked session does, and the
  section shows a count.

## 0.10.5

- **Sign-in now offers the account chooser.** With more than one Microsoft
  account signed in to the browser, OneDrive sign-in silently took whichever
  one was already there — and *Switch account* switched to the same account it
  had just left. Both providers now ask which account to use.
- Google keeps `consent` alongside it, which is what guarantees a refresh
  token, so nothing about existing sign-ins changes.

## 0.10.4

- **Handles a OneDrive account Microsoft has migrated.** Seen on a real
  account: every call failed 503 with `itemDisabledDueToUserContentMigration`,
  because Microsoft had moved the account's content and the `/me/drive` alias
  still pointed at the old, disabled drive. The client now recognises that
  answer, looks up the real drive by id (`/me/drives`) and re-addresses every
  call to it — automatically, once, mid-flight.
- That 503 is also **no longer blind-retried**: it is not a transient outage,
  and backing off against a disabled drive wasted fifteen seconds per call on
  an answer that could never change.
- If the explicit drive still reports the migration, the error finally says
  something a person can act on: sign in once at onedrive.live.com, which
  completes the migration, then sync again.

## 0.10.3

- **No more "Reading Google Drive…" over a OneDrive sync.** The progress label
  was hard-coded, and it was not alone: the delete dialogs, the fork prompts,
  the "on Drive" session tag and the file counts all said Drive whichever
  cloud was connected. Every string a person reads now names the provider it
  is actually talking to, and the render check refuses a OneDrive panel that
  still says "Drive" anywhere.
- The delete-everywhere confirmation also caught up with 0.9.20: it promised
  that other machines "will upload it again", which the tombstone has made
  false — they will not, and each is asked once about its own copy.

## 0.10.2

- **Switching provider is now one click, applied immediately.** The Sync
  through pills — now on every setup screen — and the provider name on the
  connected panel swap the whole stack in place: auth, backend, bookkeeping,
  engine. No window reload, no toast to notice; the panel simply repaints as
  the other provider, showing its own sign-in or its own setup screen.
- Each provider keeps **separate bookkeeping** (`state.json` for Google,
  `state.onedrive.json` for OneDrive, and their own merge bases and conflict
  folders). Watermarks and remote listings describe one store; carrying
  Google's numbers into OneDrive would have tagged sessions with sync state
  that belonged to the other cloud.
- Editing `claudeStateSync.provider` straight in settings.json now applies the
  same way, and switching is refused only while a sync is mid-flight.

## 0.10.1

- **The provider can be changed from the connected panel too.** The choice was
  only offered on the first-run screen, so anyone already signed in had to know
  the setting existed. The provider's name in the panel is now the control:
  click *Google Drive* (or *OneDrive*) to pick the other one. Each keeps its
  own sign-in, so switching costs a window reload and nothing else.

## 0.10.0

- **OneDrive can hold the sync instead of Google Drive.** The panel asks on
  first run; `claudeStateSync.provider` records the answer, and each provider
  keeps its own sign-in, so switching later costs nothing but a window reload.
- The Microsoft side is the easier one to set up: one app registration, no API
  to enable, no consent screen to publish, no 7-day testing trap — and **no
  client secret at all**, because a desktop registration is a public client
  protected by PKCE, exactly the mechanism already used for Google.
- Scope parity: `Files.ReadWrite.AppFolder` sees one folder of its own under
  `Apps/` and nothing else — the OneDrive analogue of `drive.file`.
- Under the hood the engine now talks to a backend interface with two
  implementations. OneDrive items cannot carry app metadata the way Google
  files do, so every upload gets a tiny `.meta` companion holding the same
  fields; listings fold them back in and hide them, and nothing above the
  backend can tell the providers apart. One Microsoft-specific trap is handled:
  their refresh tokens rotate on use, and the replacement is stored on every
  refresh — miss that and the sign-in dies within days.
- One machine syncs through one provider at a time; the two clouds are separate
  stores, and state pushed to one is not in the other.

## 0.9.20

**Fixes "delete everywhere" undoing itself. Upgrade every machine.**

- **A session deleted everywhere came back.** Deleting removed the copy on
  Drive and nothing else, so the next machine to sync pushed its own copy
  straight back up, and the machine that asked for the deletion pulled it back
  down. The deletion was real for about a minute.
- A deletion now leaves a marker in place of the file. Every other machine
  reads it and **stops pushing that session** — which is the half that needs no
  permission, since re-uploading it would undo somebody's decision behind their
  back.
- **And the other machines are told**, which they never were: on the next sync
  each one asks, once, whether to delete its own copy too. Declining is
  remembered rather than asked again. Nothing else here removes a local
  transcript without being asked and this does not either — but silence is no
  longer mistaken for consent to put it back.
- The delete dialog says all of that now, instead of "for every machine",
  which was a promise the code did not keep.

## 0.9.19

- **A transcript that was already damaged can be copied again.** The copy check
  refused any result containing replacement characters, on the reasoning that
  the rewrite must have produced them. Sometimes it had not: the chunk-decoding
  bug fixed in an early version left U+FFFD inside real conversations, and
  those characters are still in those files. Refusing to copy them made the
  damage permanent — the conversation could never be moved anywhere, ever.
- The check now compares against the source: only damage **the copy
  introduced** fails it, and a copy that reproduces existing damage exactly is
  correct, because that is what a faithful copy of a damaged file looks like.
- What was already there is reported rather than hidden — the copy says how
  many records carried damage in, so you learn the state of the conversation
  instead of learning nothing.

## 0.9.18

- **The updates banner names the project**: *1 session in acme/web from
  laptop*. Drive answers the update query with a flat list of files and no
  folder in it, so a push to a project you did not have open read exactly like
  one to the project you did — which is a surprise every time.
- **And it no longer offers to pull what this window cannot pull.** A window
  syncs the projects it has open; for anything else the banner says so —
  *open that project to sync it* — rather than showing a button that would run
  a sync doing nothing for the thing it was pointing at.
- Uploads now record which project they belong to. Files pushed before this
  fall back to the pushing machine's own folder name, so even old ones are
  named rather than anonymous.

## 0.9.17

**Fixes `not synced` appearing on sessions nobody edited. Upgrade.**

- **Opening a session tagged it — and every other session too.** A session that
  is already in sync is skipped by the sync, and the skip recorded nothing, so
  those sessions had no baseline to be compared against. With no baseline the
  panel fell back to the time of the last sync cycle, and since opening a
  session moves its modified time and nothing else, everything anyone so much
  as looked at read as edited.
- A sync now records a baseline for the sessions it **leaves alone**, not only
  for the ones it moves. That is what the comparison needed all along: after
  one sync every session has one, and the tag is exact.
- With no baseline the panel now says nothing rather than guessing from a
  timestamp. A tag that cries wolf is worse than a tag that waits one cycle.

## 0.9.16

- **A forked session is settled from the panel.** The red `forked` tag is now
  the button: click it to see both copies side by side, then keep this
  machine's or take the other machine's. Telling you to go and compare files in
  a folder was work handed back to you for no reason.
- **Nothing is deleted either way.** The copy you do not keep is parked
  alongside the one that was already there, so a decision made in a hurry is
  never the end of a conversation.
- Settling also records that this machine has seen the other side, so the next
  sync puts the kept copy on Drive instead of deferring it as "the other
  machine got there first" — which is what made a fork permanent.
- **Fixed: 0.9.15 shipped mangled punctuation.** Every em dash in the README,
  the changelog and the Marketplace description came out as `â€”`. PowerShell on
  the second machine reads a file without a byte-order mark as ANSI, so the
  bump-the-version scripting corrupted every non-ASCII character it rewrote.

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
  that could never work.

## 0.9.14

**The not-synced tag no longer cries wolf. Reported within the hour by its
first user — twice, correctly.**

- **Opening a session just to read it flipped the tag.** Claude Code touches
  the transcript when a session is opened: the mtime moves, the size does not.
  Verified against a real file — modified time newer, grown by exactly zero
  bytes. A touch is not a change; same size now reads as in sync.
- **Closing a session after syncing flipped it too**, which read as "you must
  close before you sync". Closing writes derived records — the summary, the
  title. The tag now reads only the bytes appended since the last push and asks
  whether any of them is a conversation turn (a record with a `uuid`; summaries
  carry `leafUuid`, which deliberately does not match). Titles and summaries
  travel on the next sync as they always did — quietly.
- Reading the appended bytes costs almost nothing: on a 70 MB transcript whose
  owner pressed close, the tail is a few hundred bytes, and the answer is
  cached until the file changes again.
- A transcript that *shrank* against Drive's copy is tagged rather than
  explained away — that is not what an append looks like.

## 0.9.13

- **The `not synced` tag now works from the moment 0.9.12 is installed**, not
  from its second sync. The exact comparison needs the per-session metadata
  that only a 0.9.12+ sync stores, and state written by older builds has none
  of it — so the one session the tag was built for kept reading `synced` until
  a sync happened to run. Until the metadata exists, the time of the last
  completed sync stands in: a transcript modified after it is not on Drive
  yet. With nothing known at all the panel says nothing rather than guesses.
- Excluded sessions are never tagged. Drive being behind is exactly what
  unticking asked for.

## 0.9.12

- **A session typed in after its last sync is tagged `not synced`**, in amber,
  where `synced` used to keep saying otherwise. That tag is the answer to "did
  I remember to press ⟳ before leaving?" — the one question the panel could
  not answer, since `synced` only ever meant "a copy exists on Drive", not
  "the copy is current".
- The comparison costs nothing at sync time and nothing at panel time: every
  upload already records the transcript's size and mtime on the Drive file,
  and every pull stamps the local file with the same mtime — so on a synced
  session the two clocks are equal, and a local file strictly past Drive's
  has turns Drive lacks. The panel just compares the two numbers it already
  had.
- A push now updates the in-memory Drive listing it just wrote to, so a
  session uploaded seconds ago reads as synced immediately rather than
  after the next full cycle.

## 0.9.11

- **The panel now tells you when another machine has pushed something you have
  not pulled**: a banner — *2 sessions from laptop waiting on Drive — pull
  now* — and a ⬇ count in the status bar. Until now the only way to know
  whether machine A's sync had arrived was to pull and see.
- Noticing costs one small request: Drive is asked what changed since this
  machine last looked. The decision uses Drive's own modified times as the
  watermark and the pushing machine's name recorded on every upload — so your
  own pushes are never echoed back, a skewed local clock cannot hide anything,
  and no hashes or downloads are involved in noticing. Whether a pull actually
  replaces a file is still decided the usual way: size and mtime for
  transcripts, md5 for the small files, record uuids on any dispute.
- Checked when the panel opens and every few minutes while it stays open —
  never in the background with the panel closed, never twice within a minute.
  `claudeStateSync.remoteCheck.minutes` sets the pace; 0 turns it off.
- The news persists until a sync pulls it, so noticing is not forgetting:
  re-checks cannot lose what was already announced.

## 0.9.10

**Fixes a machine that syncs and still never sees the other side's latest turns.
Upgrade both machines.**

- **A stale path rewrite could freeze a session on one machine, permanently.**
  A pulled transcript is rewritten to the local checkout by whichever build
  pulled it. If the rewrite rules have changed since — the drive-letter case
  fix, a moved project — the next pull differs from the local copy in bytes
  while being the same conversation, and the byte comparison called that a
  fork: the download was parked aside, the visible file left old. Every later
  sync repeated it. Forks are now confirmed at the record level — line count
  and the per-record uuids, which no rewrite touches — and only a fork the
  records also show is treated as one.
- **A session open on the receiving machine is no longer replaced underneath
  the window.** The running conversation keeps its state in that process, so
  the file swap changed nothing on screen while the process kept appending to
  the swapped file — "sync did nothing", with two histories interleaving on
  disk underneath. The pull now leaves a live session alone and says so in the
  log; close it, sync, resume.
- If machine B pressed sync while machine A was still uploading, B pulled
  whatever had reached Drive at that moment. The README now says to let A
  finish — the progress numbers from 0.9.4 show when that is.

## 0.9.9

- **The setup screens now carry the whole panel underneath them**, not a summary
  of it: your projects, every session by name and size, the shared scopes, the
  activity list. All of it is read from disk, so it is real and populated before
  Google is involved at all — and it answers "what am I setting this up for"
  better than any description above it does.
- Sync appears on those screens too, disabled and saying why. A button that
  materialises only once everything works gives no hint that it is the point of
  the work.
- Sign out and switch account stay hidden until there is an account to act on,
  and the Drive row says *not connected yet* rather than *signed in*.
- **Fixed: a project card said "sessions none yet" directly above its sessions.**
  The count came from the sync stats, which a sync writes — so before the first
  one it was 0 while the transcripts sat on disk in plain sight. It counts the
  rows now.
- The build pins what each screen must and must not contain, and the panel
  fixture uses the field names the panel actually reads. It had invented its
  own, so it was checking nothing about how a session draws.

## 0.9.8

- **The screen you actually land on now shows your projects and sessions too.**
  0.9.7 put them on the first-run and sign-in screens but not on the one for a
  sign-in whose OAuth client has gone — which is the screen an existing install
  lands on after an update, so in practice nobody saw them.
- The build now pins what each screen must say, not only that it draws without
  throwing. A setup screen missing the list looks perfectly healthy to a
  renderer; it is a regression to a reader.

## 0.9.7

- **A first run now opens on what the extension does**, not on four errands in
  Google Cloud Console. Four lines — state on every machine, carrying a
  conversation across, your own Drive, nothing without asking — then the setup.
- **And on your own projects and sessions, by name.** All of that is read from
  disk and needs no Google account, so the panel can show what would be carried
  across before you have connected anything: the projects it found, the
  conversations in them and what they weigh.
- The sign-in step shows the same, so the last screen before connecting says
  what connecting is for.
- **The build now draws the panel rather than only parsing it.** A missing
  function or a property read off nothing throws when the panel renders, which
  ships as a blank sidebar with a clean build — that has happened here once
  already. `check-webview.mjs` runs the panel in every state it has, including a
  machine with no projects, and fails on a throw, on nothing drawn, and on
  `undefined` or `NaN` reaching the markup.

## 0.9.6

- **The client is entered in the panel now**, in two boxes with a Save button,
  instead of two prompts that appear one after the other at the top of the
  window. Enter saves from either box — in the prompts it silently did nothing
  when the ID had not been recognised, which reads exactly like a key that does
  not work.
- **Paste the whole `client_secret….json`** Google gives you into either box and
  both fill in. It is the file you downloaded; picking two fields out of it by
  eye was work with no purpose. The command in the palette takes it too, and
  stops asking for the secret when the JSON already carried one.
- Why the prompts were the wrong shape: the second one asked for the secret only
  after the first was accepted, so anyone who did not have it to hand pressed
  Escape and nothing was saved — with no sign that nothing had been. Both boxes
  are visible at once and say what is wrong, next to the box that is wrong.
- **Typing into the panel is no longer interrupted by the panel.** It repaints on
  its own — a session opening is enough — and that used to wipe a half-typed
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
  fixes it: paste the same client back in. **No new sign-in is needed** — a
  refresh token belongs to the client that issued it, so the same client picks
  the session up where it left off. The sign-in button, which is what the old
  message sent you to, could never have fixed this.
- The two failures are told apart everywhere — panel, Sync now and the error
  itself. `authReadiness()` decides it in one place, and a test pins all four
  combinations.

## 0.9.4

- **A sync now says how far along it is** — the percentage and the file count
  beside the name of the file being worked on. The fraction was already being
  computed; it only ever set the width of a 2px bar, which on a long sync is
  indistinguishable from a spinner.
- **Fixed: the bar never showed the indeterminate phase.** It is drawn that way
  while Drive is being listed, because the total cannot be counted before then —
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
