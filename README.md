# Claude State Sync — releases

[![Marketplace](https://img.shields.io/badge/Marketplace-install-0098FF)](https://marketplace.visualstudio.com/items?itemName=triplepai14.claude-state-sync)
![Version](https://img.shields.io/badge/version-0.9.10-blue)
![VS Code](https://img.shields.io/badge/VS%20Code-1.85+-007ACC)
![Storage](https://img.shields.io/badge/storage-your%20own%20Google%20Drive-4285F4?logo=googledrive&logoColor=white)
![Scope](https://img.shields.io/badge/OAuth%20scope-drive.file-34A853)
![Servers](https://img.shields.io/badge/third--party%20servers-none-brightgreen)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

Keep Claude Code's working state — memory, skills, plans, `CLAUDE.md` and session
transcripts — in step across machines, through **your own** Google Drive. No
third-party server, and nothing syncs unless you ask it to. It also copies a
conversation from one project into another, rewriting the paths recorded inside
the transcript so it belongs there.

Problems and requests: [open an issue](https://github.com/triplepai14/Claude-State-Sync-Releases/issues).

![The Claude State Sync panel: account, project, session list with checkboxes, shared scopes and activity](https://github.com/triplepai14/Claude-State-Sync-Releases/raw/main/media/screenshot-panel.png)

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

Everything below is a **one-time job**, and only the Google side takes any
effort. Budget five minutes.

## Why you have to do this at all

Google will not let an application touch a Drive unless that application is
registered with Google. The registration is called an **OAuth client**, and only
a human with a Google account can create one. There is no way for an extension
to skip it.

Extensions that appear to skip it have not: their developer registered a client
and shipped it inside the extension, so every user's traffic runs through the
developer's registration. This extension does not do that, because it would put
your Drive access on someone else's quota and let anyone holding the extension
file raise a Google consent screen in their name. You register your own instead.

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
   to <your app name>**. This is expected: the unverified developer is you.
4. Approve. The browser says you can close the tab.

Repeat step 5 on every other machine, with the same client and the same Google
account. Steps 1–4 are never repeated.

### Only the `drive.file` scope

The extension asks for `drive.file`, which can see **only files it created
itself**. The rest of your Drive is invisible to it — not by policy, but because
Google will not return those files to this client at all.

---

# Daily use

Press **⟳** in the panel to sync. A sync happens when you ask for one, when you
sign in, or when you switch a session back on — never on a timer. While it runs
the panel shows the file being worked on, the percentage and the count, so a
long sync is not a spinner you have to guess at.

The panel itself does keep up to date on its own, so a session you start or
close shows up without you doing anything. That only re-reads local files; it
never talks to Drive.

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

**Conflicts:** files that changed on both sides are merged with `git merge-file`.
Overlapping edits leave your local file untouched and raise a conflict in the
panel, with a local ↔ remote diff and *Keep Local* / *Take Remote* / *Open
Merged*.

**Sessions continued on two machines** are compared by content. If one is simply
further along it wins; if they genuinely forked, both are kept — the incoming
copy is parked in `~/.claude-state-sync/forks/`, outside the folder Claude Code
scans, so `claude --resume` still shows one entry.

# Limitations

- Transcripts are an internal Claude Code format that can change between
  releases.
- Sessions are moved, not merged. Forks are kept side by side, never stitched.
- A file deleted on another machine is re-pushed from here rather than deleted;
  local files are never removed automatically.
- Only open workspace folders sync per-project state. Global scopes always sync.
- Transcripts contain your prompts, replies and file contents Claude read. They
  land in your Google Drive — treat that folder as sensitive.

# License

MIT
