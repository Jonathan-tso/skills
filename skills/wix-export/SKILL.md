---
name: wix-export
description: Export a Wix site or create a new one with the origami CLI, each in its own local folder, then continue straight into the change the user asked for. Installs, updates or signs in to the CLI only when that is missing. Use when the user wants a Wix site in a local folder, is starting with Origami, or origami is missing, outdated or signed out. Never imports or publishes.
---

# Wix export

Goal: get the user's Wix site into its own folder under `wixsites` in the user's home folder, ready to edit, then carry on with what the user asked for.
Setup is a means, not the result: run only the steps that are missing, and never end the turn at the export.

## 1. Check what is already done

Run one command first:

```bash
origami whoami
```

- It prints an email address: Node, the CLI and sign-in all work. Go to [Get the site](#4-get-the-site) and skip the machine checks and the install.
- It prints no email address: go to [Sign in](#3-sign-in).
- `origami` is not found: go to [Install the CLI](#2-install-the-cli).

## 2. Install the CLI

Origami needs Node 22.16 or later and Corepack.
The CLI installs from `https://npm.dev.wixpress.com/`, which works only on the Wix network or VPN.
Check all three in one command, then install:

```bash
node -v; corepack -v; npm view @wix/origami-cli version --registry=https://npm.dev.wixpress.com/
npm install -g @wix/origami-cli --registry=https://npm.dev.wixpress.com/
```

- Install Node or change its version yourself once the user approves, and never run npm with `sudo`.
- When Node works but `npm view` fails, ask the user to connect to the Wix network or VPN.
- When a later command prints a CLI update diagnostic, run `origami update` and retry that command.

## 3. Sign in

- Run `origami login`, give the user the URL and the code it prints, and wait for `Logged in as …`.
- When a later command fails with a sign-in or authorization error, sign in again and retry that command once.
- Never type the user's password.

## 4. Get the site

- Put every value the user gave (site name, folder) in single quotes in the command, writing each `'` inside it as `'\''`, so the shell never expands it. Write the home folder as its absolute path, since `~` does not expand inside quotes.
- Set `<site>` to the site's own folder under `wixsites` in the user's home folder: the site name with each run of spaces, `/` or `\` replaced by `-`, such as `<home>/wixsites/Bloom-Florist`. Create `<home>/wixsites` when it is missing. Use another folder only when the user names one.
- Before you export or create a site, tell the user in one line that the export adds a web OAuth app named `export site` to a site that has none.
- For a site the user names, narrow the list with `origami sites | grep -iF -- '<name or msid>'`; the full list can hold hundreds of sites. Keep only rows whose name or msid equals the user's words exactly, and ask the user when none or several remain. Then:
  - When `<site>` already holds an export (`packages/site-app/.env`) whose `VITE_WIX_SITE_ID` is that msid, reuse it and skip the export. Go to [Continue with the user's request](#6-continue-with-the-users-request) when `<site>/node_modules/@wix/origami-skills/skills/wix-site-edit/SKILL.md` exists; otherwise ask the user before running `yarn install` in it, then go to [Install the site's packages](#5-install-the-sites-packages).
  - When `<site>` exists and holds anything else, add a number to the folder name, such as `Bloom-Florist-2`.
  - Otherwise run `origami export-site --site <msid> -o '<site>' && cd '<site>' && yarn install`.
- For a new site, add a number to the folder name when `<site>` exists and is not empty, then run `origami create-site --name '<name>' -o '<site>' && cd '<site>' && yarn install` once; it creates a blank, unpublished Studio 2 site and exports it.
- When `create-site` prints `was created but isn't ready to export yet`, run the `origami export-site` command it prints a minute later; never run `create-site` again for that site.
- When the export fails without detail, export once more into a new empty folder.
- Report each `Warning: no component wrapper for …` line to the user.

## 5. Install the site's packages

- When the install did not already run with the export, run `yarn install` in `<site>`.
- When `yarn` is not found, run `corepack enable`, then run `yarn install` again.
- When the install fails, report the command and the error, and do not change the exported packages to make it pass.

## 6. Continue with the user's request

Read `<site>/node_modules/@wix/origami-skills/skills/wix-site-edit/SKILL.md` now, in the same turn, then:

- The user's request names a change (content, design, a page, a section, a new site's purpose): make it now with `wix-site-edit`. Do not stop to report the setup; mention it in one line of the final answer.
- The request names no change: read the exported pages and theme, then ask what to change, offering 2–4 concrete options that fit this site, such as restyling the theme, rewriting the home page for the user's business, or adding a page. Use a multiple-choice question tool when you have one.
- When you cannot write to `<site>` from where you run, tell the user the site is in `<site>` and to open that folder as the project and send the request there.

Never publish the site.
