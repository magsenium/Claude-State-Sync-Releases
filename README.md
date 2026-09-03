# Claude State Sync — releases

[![Marketplace](https://img.shields.io/badge/Marketplace-install-0098FF)](https://marketplace.visualstudio.com/items?itemName=triplepai14.claude-state-sync)
![Version](https://img.shields.io/badge/version-0.14.1-blue)
![VS Code](https://img.shields.io/badge/VS%20Code-1.85+-007ACC)
![Storage](https://img.shields.io/badge/storage-your%20Drive%2C%20OneDrive%20or%20git%20repo-4285F4)
![Scope](https://img.shields.io/badge/OAuth%20scope-app--scoped%20files%20only-34A853)
![Servers](https://img.shields.io/badge/third--party%20servers-none-brightgreen)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

Keep Claude Code's working state — memory, skills, plans, `CLAUDE.md` and session
transcripts — in step across machines, through **storage you already own**. No
third-party server, and nothing syncs unless you ask it to. It also copies a
conversation from one project into another, rewriting the paths recorded inside
the transcript so it belongs there.

## Three places to keep it — pick one, press one button

| | What you do | Where your files land |
| --- | --- | --- |
| **Google Drive** | Press **Sign in** | A `ClaudeStateSync` folder in your Drive |
| **OneDrive** | Press **Sign in** | This app's own folder in your OneDrive |
| **A git repository** | Paste a repo URL | A private repository of yours, any host |

No API key to generate and no console to visit: the extension carries its own
Google and Microsoft registrations, and the git store needs no account at all.
Switch between them whenever you like — each keeps its own sign-in, so nothing
is lost by changing your mind.

Problems and requests: [open an issue](https://github.com/magsenium/Claude-State-Sync-Releases/issues).

![The Claude State Sync panel: account, project, session list with checkboxes, shared scopes and activity](https://github.com/magsenium/Claude-State-Sync-Releases/raw/main/media/screenshot-panel.png)

## What gets synced

| Local path | Scope | Default |
| --- | --- | :-: |
| `~/.claude/CLAUDE.md` | global | on |
| `~/.claude/plans/**`, `skills/**`, `agents/**`, `commands/**` | global | on |
| `~/.claude/settings.json` | global | **off** |
| `~/.claude/projects/<project>/memory/**` | per-project | on |
| `~/.claude/projects/<project>/*.jsonl` (transcripts) | per-project | on |

Projects are matched by **git remote**, so the same repo lines up even when it
sits at a different path on each machine. `settings.json` is off by default
because most of it is machine-specific; when on, home paths are stored as
`${HOME}` and expanded again on the way down.

Never synced: `cache/`, `backups/`, `ide/`, `shell-snapshots/`, `session-env/`,
`telemetry/`, `plugins/`, `.credentials.json`, `.env`, and dotfiles.

---

# Setup

Install the extension, open the panel — the cloud icon in the activity bar — and
pick where the state should live. That screen is the whole setup:

![The setup screen: Sync through Google Drive, OneDrive or Git repo, and a single Sign in button](https://github.com/magsenium/Claude-State-Sync-Releases/raw/main/media/screenshot-setup.png)

- **Google Drive** — press **Sign in to Google Drive**. Your browser opens
  Google's consent screen, which asks for one permission: *files this app itself
  creates*. It cannot see anything else in your Drive.
- **OneDrive** — press **Sign in to OneDrive**. A work or school account may need
  an administrator to approve the app first; a personal Microsoft account never
  does.
- **A git repository** — paste the URL of an empty **private** repository. It uses
  the git already on your machine, with your own credentials, so there is nothing
  to sign into. See [A git repository instead](#a-git-repository-instead).

Then repeat on your other machines: same account, or same repository. Nothing
syncs until you press **sync this project**.

Rather use an OAuth client of your own? Press **paste one** on the sign-in
screen — [for Google](#registering-a-google-client-of-your-own-optional), [for
Microsoft](#onedrive-instead). You do not need to.

## Registering a Google client of your own (optional)

You do not need this. Do it if you would rather not use the shipped
registration — an employer that blocks unapproved apps, a wish to keep the
traffic entirely on your own quota, or simply preferring to trust nothing you
did not create.

On the sign-in screen press **paste one**, next to "Rather use your own Google
OAuth client?", and follow the steps below to make one. A pasted client takes
over from the shipped registration on its own; `claudeStateSync.oauth.useBuiltin`
only matters if you want the built-in one back without deleting yours.

> **Switching between the two starts a separate store.** A client is an
> application in Google's eyes, and `drive.file` shows an application only the
> files it created itself — so the other client's uploads become invisible, the
> panel reads empty, and the next sync writes a second `ClaudeStateSync` folder.
> Nothing is deleted, and switching back brings the old store into view. To move
> a store across, sync everything down first, switch, sync up again, then delete
> the old folder in Drive by hand.
>
> The account row says which one a machine is on — **built-in client** or **own
> client** — so two machines that share an account but cannot see each other's
> uploads can be told apart at a glance.

You are creating **two separate things**, and it helps to keep them apart:

| | What it is | Who it identifies |
| --- | --- | --- |
| **OAuth client** | An ID and a secret, created once in Google Cloud Console | The *application* |
| **Sign-in** | The browser consent screen you approve | *You*, granting that application access to *your* Drive |

Step 1–4 below create the first. Step 5 does the second.

## 1. Create a Google Cloud project

Go to <https://console.cloud.google.com/projectcreate>, give it any name
(`claude-state-sync` is fine), and create it.

"Cloud project" sounds like a server you rent. It is not — nothing runs there.
It is a container for the registration. It is free and needs no credit card.

## 2. Enable the Google Drive API

Go to <https://console.cloud.google.com/apis/library/drive.googleapis.com>,
check the project selector at the top is your new project, and press **Enable**.

Skip this and sign-in works but every sync fails with "API not enabled".

## 3. Publish the consent screen — do not skip this

Go to <https://console.cloud.google.com/auth/audience>.

Set **User type** to **External**, then press **Publish app** so the publishing
status reads **In production**.

- *External* does not mean "public". It means "any Google account may approve
  it". Nobody else can, because nobody else has your client ID.
- *Internal* is greyed out unless your account belongs to a Google Workspace
  organisation. That is expected, and External is the one you want.
- **If you leave it on "Testing", Google expires your sign-in after 7 days** and
  you will be signing in again every week, with no explanation of why.
- No Google review is needed. Review applies to sensitive scopes; this extension
  asks only for `drive.file`, which is not one.

While you are here, **Branding** takes an app name and a support email. The app
name is what you will see on the consent screen in the next step.

## 4. Create the OAuth client

Go to <https://console.cloud.google.com/auth/clients> → **Create client** →
application type **Desktop app** → **Create**.

You get a **Client ID** ending in `.apps.googleusercontent.com` and a **Client
secret**. Both stay visible in the console, so there is no need to write them
down carefully.

The "secret" is not really secret here — Google's own guidance for desktop
applications says it is embedded in the application and "obviously not treated
as a secret". The sign-in is protected by PKCE and a loopback redirect instead.

## 5. Put them into the extension and sign in

Open the **Claude State Sync** panel (the cloud icon in the activity bar). It
lists the same four steps with buttons that open each page, then:

1. Fill in the **Client ID** and **Client secret** boxes and press **Save** —
   or paste the whole `client_secret….json` Google gave you into either box and
   both fill themselves in. They are stored in VS Code's secret storage —
   Credential Manager on Windows, Keychain on macOS, libsecret on Linux — never
   in `settings.json`.
2. Press **Sign in to Google Drive**. Your browser opens Google's consent
   screen.
3. If you see **"Google hasn't verified this app"**, choose **Advanced** → **Go
   to <your app name>**. The warning is about the registration being
   unverified, not about where your files go.
4. Approve. The browser says you can close the tab.

Repeat step 5 on every other machine, with the same client and the same Google
account. Steps 1–4 are never repeated.

With the shipped registration — the default — none of this applies: sign in on
each machine with the same Google account, and that is the whole of it.

### Only the `drive.file` scope

The extension asks for `drive.file`, which can see **only files it created
itself**. The rest of your Drive is invisible to it — not by policy, but because
Google will not return those files to this client at all.

## OneDrive instead

The shipped registration covers OneDrive too, so this is optional — do it to
keep the traffic under a registration of your own, or because your organisation
only allows apps it registered itself. Press **paste one** on the sign-in screen
when you have the id; a pasted registration takes over from the built-in one.

Register once with Microsoft:

1. Go to <https://aka.ms/AppRegistrations> → **New registration**. Any name.
   Under **Supported account types** choose the option ending in **"and
   personal Microsoft accounts"** — that is what lets your own OneDrive sign
   in.
2. In the registration: **Authentication** → **Add a platform** → **Mobile and
   desktop applications**, and add `http://localhost` and `http://127.0.0.1`
   as custom redirect URIs.
3. Copy the **Application (client) ID** from the Overview page — a GUID — and
   paste it into the panel. That is the whole registration: a desktop app is a
   *public client*, so **there is no secret**, and the sign-in is protected by
   PKCE and a local redirect instead.
4. Press **Sign in to OneDrive** and approve.

The scope is `Files.ReadWrite.AppFolder`: the extension sees **one folder of
its own** under `Apps/` in your OneDrive and nothing else, the OneDrive
analogue of `drive.file`. Switching between them is one click in the
panel and applies immediately — each provider keeps its own sign-in and its
own bookkeeping, so nothing is lost either way — but the two clouds are separate stores, and
state pushed to one is not in the other.

### When OneDrive refuses the app folder

Microsoft has been moving personal accounts to a newer storage backend, and on
some of them the app folder is refused outright — `400`, or `accessDenied` with
`serviceReadOnly`, whatever the account's storage says. Those accounts have no
working app folder to sync into.

The panel offers **use an ordinary folder** on the error, and
`claudeStateSync.onedrive.folderMode` holds the same choice. It keeps the store
in a plain `ClaudeStateSync` folder in your OneDrive instead — which needs
`Files.ReadWrite`, a permission that **can see your whole drive**. That is a
real trade, so it is never taken for you: the default stays the narrow scope,
switching needs a new sign-in, and the two folders are separate stores. Google
Drive and a git repository need no such thing.

## A git repository instead

Pick **Git repo** in the panel and paste a repository URL. That is the entire
setup: no account to register, no OAuth client, no token of ours anywhere.

1. Make an **empty private repository** on any host — GitHub, GitLab, a server
   of your own. Private matters: transcripts are your conversations.
2. Paste its URL (`https://…` or `git@…`) into the panel and press **Connect the
   repository**.
3. Do the same on every other machine.

The extension clones it into its own state directory and works there: each sync
fetches the repository, writes what changed, then commits and pushes. Access is
**your** git — the credential helper that answers `git push`, your SSH agent,
your host's rules. Nothing is stored by the extension to reach it, and a host
that only lets you in over SSH is fine.

Worth knowing before choosing this one:

- **A repository keeps everything for ever.** Transcripts are the bulk of what
  syncs, and git stores each version, so the repository grows with the history
  in a way a Drive folder does not. Use a repository kept for this and nothing
  else, and remake it if it ever gets uncomfortable — nothing here needs the
  history.
- Two machines syncing at the same moment: the second push is refused, dropped,
  and retried on the next sync. Nothing is lost and nothing is force-pushed.
- The branch is `main` unless `claudeStateSync.git.branch` says otherwise; it is
  created on the first push if the repository is empty.
- Every commit is authored as *Claude State Sync*, never your git identity.

---

# Daily use

Press **⟳** in the panel to sync. A sync happens when you ask for one, when you
sign in, or when you switch a session back on — never on a timer. While it runs
the panel shows the file being worked on, the percentage and the count, so a
long sync is not a spinner you have to guess at.

The panel itself does keep up to date on its own, so a session you start or
close shows up without you doing anything. That only re-reads local files; it
never talks to Drive.

**When another machine has pushed something you have not pulled, the panel says
so**: *2 sessions in acme/web from laptop waiting on Drive — pull now*, and the
status bar
gains a ⬇ with the count. Noticing is one small request — Drive is asked what
changed since this machine last looked, using Drive's own modified times and
the pushing machine's name recorded on every upload, so your own pushes are
never echoed back and a skewed clock cannot hide anything. It runs when the
panel opens and every few minutes while it stays open; nothing is downloaded
until you pull. If the push is for a project this window has not got open,
**pull it here** adopts it — it finds the folder by matching git remotes, asks only if it cannot, and syncs
from then on, until *stop syncing this one* on its card. `claudeStateSync.remoteCheck.minutes` sets the pace (0 turns it
off).

**Handing a conversation to another machine:** press ⟳ before you leave machine
A — and let it finish; what B can pull is whatever had reached Drive when B
looked. On machine B open the project, press ⟳, then run `claude --resume` in a
terminal. The session is in the picker with every path inside it already
rewritten to B's checkout. If that session is already **open** on B, it is left
untouched until it closes — a running conversation keeps its state in the
window, so replacing the file underneath would only disagree with what you see.
Close it, press ⟳, resume.

**Session names** come from the transcript itself — the name you set with
`/rename`, else the summary Claude derives, else the first prompt. A session
that is **running right now** is marked `live` and shows its first prompt: while
a session is open Claude keeps its title in memory and only writes it into the
transcript when the session ends, so until then there is nothing on disk to
read. Close the session and the name catches up.

**Choosing what syncs:** each project lists its sessions, labelled by
conversation rather than by UUID. Untick one to stop it moving in either
direction — useful for a 70 MB transcript you do not want on a laptop. The
trash icon deletes a session here, or here and on Drive; the one beside it
copies a session into another project, rewriting every path inside the
transcript so it belongs there. That one copies rather than moves on purpose —
the rewrite touches every line of the only record of a conversation, so the
result is checked and the original is left alone for you to delete afterwards.
Neither is offered while a session is running.

**Each session says where it stands**: `synced`, or `not synced` in amber the
moment a conversation gains turns Drive lacks — the reminder to
press ⟳ before switching machines. `local only` and `on Drive` mean only one
side has it at all, and `live` marks a session running right now. Synced-ness
is read off numbers the sync already keeps — the mtime recorded on the Drive
copy against the file's own — so the tag costs nothing to show.

**Conflicts:** files that changed on both sides are merged with `git merge-file`.
Overlapping edits leave your local file untouched and raise a conflict in the
panel — click **resolve** and choose: compare the two side by side, keep this
machine's copy, take the one from the cloud, or edit a merged copy by hand when
one could be built. Nothing is written until you pick.

**Sessions continued on two machines** are compared by content. If one is simply
further along it wins. If they genuinely forked, neither copy contains the other
and no sync can merge them, so the row is tagged `forked` in red — **click that
tag** to settle it: compare the two side by side, then keep this machine's copy
or take the other machine's. Whichever you choose, the copy you did not keep is
parked in `~/.claude-state-sync/forks/`, outside the folder Claude Code scans —
so nothing is deleted and `claude --resume` still shows one entry.

# Limitations

- Transcripts are an internal Claude Code format that can change between
  releases.
- Sessions are moved, not merged. Forks are kept side by side, never stitched.
- A *file* deleted on another machine is re-pushed from here rather than
  deleted; local files are never removed automatically. **Sessions are the
  exception**: "Delete everywhere" marks the session deleted on Drive, so no
  machine pushes its copy back, and each one is asked once whether to remove
  its own — asked, not told, because nothing here deletes a conversation on a
  machine whose owner did not say so.
- Only open workspace folders sync per-project state. Global scopes always sync.
- Transcripts contain your prompts, replies and file contents Claude read. They
  land in your Google Drive — treat that folder as sensitive.

# License

MIT
