# The Living Path — project notes and working rules

This file records how this repo works and how changes get made. It is here so the same
conventions apply in every session, on every machine, without anyone having to remember them.

## The project

- **Repo:** `git@github.com:gurudevak/the-living-path.git` (owner: `gurudevak`)
- **Live site:** https://thelightofthesoul.co.uk
- **Deployment:** GitHub Pages serves the site directly from the root of the `main` branch.
- **Custom domain:** the `CNAME` file in the repo root contains `thelightofthesoul.co.uk`. That file
  **is** the custom-domain setting — deleting or renaming it takes the site off the domain. HTTPS is
  enforced, so `http://` redirects to `https://`. `www` redirects to the bare domain.

> **Anything merged to `main` is published to the public internet within about a minute.**
> There is no staging environment. Treat `main` as the live site, because it is.

## The domain and email — read before touching DNS

The web hosting and the email for `thelightofthesoul.co.uk` are in **two different places**, and
that is the single most breakable thing about this setup:

- **Web** is on GitHub Pages (the four `185.199.10x.153` A records on the apex, `www` a CNAME to it).
- **Email** is still at **Krystal**, who also register the domain and host its DNS
  (nameservers `ns1.krystal.uk` / `ns2.krystal.uk`).

**Never** change the nameservers, **never** cancel the Krystal hosting (the mailboxes go with it),
and **never** edit the MX records, the SPF or DKIM TXT records, or the `mail` and `ftp` A records.
`mail.thelightofthesoul.co.uk` is deliberately an A record, not a CNAME — as a CNAME it would follow
the apex to GitHub and silently break every configured mail client.

Krystal's DNS is edited in **cPanel** (Hosting > My Services > the package > Zone Editor), *not* the
"Manage DNS" option in the krystal.io client area — that is a different product, and creating a zone
there could strand the MX records. If you cannot see the MX records, you are in the wrong editor.

The zone TTL is 14400 (4 hours), so a local resolver can serve stale answers for a while. Check
against the authoritative nameserver to bypass caches:

```
dig +short A thelightofthesoul.co.uk @ns1.krystal.uk
```

## Structure

- One file does almost everything: `index.html` (~1840 lines) — a single embedded `<style>`
  block and a single embedded `<script>` block, vanilla JavaScript.
- **No build step, no dependencies, no package manager.** What is in the repo is what is served.
- Images and one video (`movie.mp4`) sit beside `index.html` in the repo root. Referenced by the
  page: `logo.png`, `favicon.png`, `guru-deva.jpg`, `harnal-portrait.jpg`, `northendfarm.jpeg`,
  `eventkymodule.jpg`, `yoga-enthusiasm.jpg`.
- `cross.jpeg` and `atthesea.jpeg` are **not referenced anywhere in `index.html`**. They are kept in
  the repo but unused — don't assume they are live assets, and check with the repo owner before
  deleting them.
- Some decorative backgrounds are **hot-linked from Unsplash and Pexels** rather than stored here.
  They are outside our control; if one disappears, a section loses its background image.
- Source copy also lives in the repo as `The_Living_Path_Final_Version.md` / `.docx`. These are
  reference documents only — nothing builds from them, and the live page has moved ahead of them.

## Local preview

There is no build step, so just serve the folder statically:

```
python3 -m http.server 8080
```

Then open http://localhost:8080. The same thing is configured in `.claude/launch.json` under the
name `site` — start that instead if the preview tooling supports it.

**Always verify a change in the local preview before opening a pull request — including at a narrow
mobile viewport, around 375px wide.** A significant amount of the CSS is mobile-specific, so a change
that looks correct on a desktop window can still be broken on a phone.

Breakpoints actually used in `index.html`:

- `900px` — the main responsive breakpoint; grids collapse and stacking begins.
- `600px` — mobile-specific overrides, spread across several separate blocks.

See `.claude/handover-mobile-review.md` for a full mobile review checklist and a list of known
tight spots (pricing table at very narrow widths, the element image grid, the hero meta box). That
checklist is **still outstanding** — it has not been worked through.

## Claude Code settings

- `.claude/settings.json` — shared settings, committed, applies to everyone.
- `.claude/settings.local.json` — per-machine, **not** committed.
- `.claude/launch.json` — the `site` preview server.

Keep machine-specific absolute paths out of committed files. A path like `/Users/someone/projects/...`
is wrong the moment the repo is cloned somewhere else, and it has already caused confusion here.

## Working rules

`main` is a **protected branch**, but the protection is weaker than it used to be. As checked on
29 August 2026 it blocks force pushes and deletion and requires review conversations to be resolved.
It does **not** require a pull request, it does **not** require an approving review, and it does
**not** restrict who may push.

**So a direct push to `main` now succeeds, and publishes to the live site within about a minute.**
An earlier version of this file said the server would reject it. That is no longer true, and the
mistake it invites is a public one. GitHub will not catch an error here any more — the cycle below
is a discipline we keep on purpose, not a rule that is enforced for us. Follow it anyway.

### The cycle for every change, without exception

1. **Start from an up-to-date `main`:**
   ```
   git checkout main && git pull
   ```
2. **Create a branch.** Name it `feature/` followed by a short hyphenated description — matching what
   the repo already uses, e.g. `feature/module1-content-updates`, `feature/module2-waitlist`,
   `feature/module3-dates`, `feature/faq-copy-fix`.
3. **Make the change.** Keep each branch to one coherent piece of work. Two unrelated requests get two
   branches, so either can be approved without waiting on the other.
4. **Commit** with a message that describes the change in plain language. The repo owner reads these
   to understand what happened.
5. **Push the branch and open a pull request against `main`,** with a description covering what
   changed, why, and how it was verified. Say so explicitly if anything is visually risky or if a
   decision was uncertain. **Open the pull request as a matter of course — do not ask first.**
6. **Verify, then merge it yourself.** The repo owner has explicitly asked that pull requests be
   opened and merged without waiting for her approval, using your own judgement (confirmed
   29 August 2026). She is not a programmer and does not want to be the one clicking merge.

   Because there is no staging environment, **your verification is the only safety net** — it
   replaces the review that used to happen here. So merging is conditional on having actually
   checked the change in the local preview, at desktop **and** at ~375px, with no console errors.
   Verify first, merge second. Never merge something you have not looked at.

   Do not try to *approve* your own pull request — GitHub does not allow it and the errors are
   confusing. Merge it directly (`gh pr merge <n> --merge --delete-branch`).

   **Still pause and ask** when the change is genuinely irreversible, when it touches DNS, the
   `CNAME`, or anything in the domain and email section above, or when a design decision is a real
   coin-flip. Using judgement means occasionally judging that something needs her eyes — that is
   not the same as asking permission for routine work.
7. **After it is merged,** return to `main`, pull, and delete the local branch before starting the next
   task.

### Never

- **Never** `git push --force` anywhere in this repo.
- **Never** commit `.DS_Store` (it is in `.gitignore` — keep it that way).
- **Never** try to formally *approve* your own pull request. Merging your own is expected here (see
  step 6); approving it is a different action and GitHub refuses it.
- **Never** merge a change you have not verified in the local preview at both widths.
- **Never guess at a merge conflict in `index.html`.** Explain the situation in plain terms and check
  with the repo owner before resolving.

## When the owner drops new files into the folder

New files that have just been dragged in are **untracked** — git has no record of them. Anything
that tidies the working tree can sweep them away, and an automatic pre-switch `git stash` did
exactly that on 29 August 2026 with three photographs. From where she was sitting they had simply
vanished from the folder, which was alarming.

So: **when new images or assets appear, commit them onto a branch straight away**, before doing
anything else with them. Committing is what makes them safe.

If files ever do go missing, they are almost certainly not lost — check in this order:

```
git stash list                 # then: git show --stat 'stash@{0}^3'   (untracked files live in ^3)
git fsck --lost-found --dangling
```

Note that switching branches does **not** delete untracked files on its own — the sweep does. Say
so plainly rather than letting her think a branch switch destroyed her work.

## Commit identity

Each clone sets its own author details so changes are attributed to the right person, rather than to
whatever global default happens to be on the machine:

```
git config user.name "<name>"
git config user.email "<email>"
```

This is public and permanent in the history, so confirm it before the first commit on a new machine.
