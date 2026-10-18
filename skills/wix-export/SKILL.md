---
name: wix-export
description: Export a Wix site or create a new one with the origami CLI, and install, update or sign in to the CLI only when that fails; when the request also describes the site's content or look, continue straight into building it. Use when the user is starting with Origami, wants a site in a local folder, or origami is missing, outdated or signed out. Never imports or publishes.
---

# Wix export

Goal: put the user's Wix site in a local folder that is ready to edit. Try the export or creation first; check the machine, update the CLI and sign in only when an `origami` command fails for one of those reasons.

## 1. Get the site

- Set `<site>` to a new folder path, such as `./my-site`.
- Before you export or create a site, tell the user that the export adds a web OAuth app named `export site` to a site that has none.
- For a site the user names, find it in `origami sites` by exact name or msid, ask the user when none or several match, and run `origami export-site --site <msid> -o <site>`.
- For a new site, run `origami create-site --name <name> -o <site>`; it creates a blank, unpublished Studio 2 site and exports it.
- When `create-site` prints `was created but isn't ready to export yet`, run the `origami export-site` command it prints a minute later.
- Once `create-site` has printed `Created …`, never run it again for that site; finish with `origami export-site --site <msid> -o <site>` instead.
- When the export fails without detail, export once more into a new empty folder.
- Report each `Warning: no component wrapper for …` line to the user.
- When any `origami` command fails with `command not found`, an update diagnostic, or a sign-in or authorization error, go to [Set up the CLI](#2-set-up-the-cli-only-when-step-1-fails), then repeat this step once.
- Otherwise go to [Install the site's packages](#3-install-the-sites-packages).

## 2. Set up the CLI, only when step 1 fails

Origami needs Node 22.16 or later and Corepack. The CLI installs from `https://npm.dev.wixpress.com/`, which works only on the Wix network or VPN.

Do only the parts the failure calls for:

- **CLI missing or outdated:** check the machine, then install or update the CLI:

  ```bash
  node -v
  corepack -v
  npm install -g @wix/origami-cli --registry=https://npm.dev.wixpress.com/
  ```

  - Install Node or change its version yourself once the user approves, and never run npm with `sudo`.
  - When Node works but the install cannot reach the registry, ask the user to connect to the Wix network or VPN.
- **Signed out or unauthorized:** run `origami login`, give the user the URL and the code it prints, and wait for `Logged in as …`.
- Never type the user's password.

## 3. Install the site's packages

- In `<site>`, run `yarn install`.
- When `yarn` is not found, run `corepack enable`, then run `yarn install` again.
- When the install fails, report the command and the error, and do not change the exported packages to make it pass.

## 4. Done

- To make changes to the site, read `<site>/node_modules/@wix/origami-skills/skills/wix-site-edit/SKILL.md`.
- When the request that invoked this skill also describes what the site should contain or look like, read that file now and build the described site without waiting for another request.
- Otherwise tell the user the site is in `<site>` and they can now ask for changes to it.
- Never publish the site.
