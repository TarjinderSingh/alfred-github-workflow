# Building and Maintaining the Custom Alfred GitHub Workflow

This project does not require a traditional compilation step. The included [`bin/build`](https://github.com/TarjinderSingh/alfred-github-workflow/blob/fix/copy-ssh-clone-url/bin/build) script packages the PHP source into an installable `github.alfredworkflow` file.

The repository is organized so that:

- `main` stays aligned with the upstream project.
- `fix/copy-ssh-clone-url` contains the two custom commits.

## Build and install

Clone the fork, switch to the customization branch, and run the build script:

```bash
git clone https://github.com/TarjinderSingh/alfred-github-workflow.git
cd alfred-github-workflow
git switch fix/copy-ssh-clone-url

# PHP 8.2 or newer is required. If necessary: brew install php
php -v

./bin/build
open ./github.alfredworkflow
```

Opening the generated file launches Alfred's normal workflow import or update dialog. Because the workflow retains its existing bundle ID, Alfred recognizes later builds as updates to the installed workflow. See [Alfred's workflow installation documentation](https://www.alfredapp.com/help/workflows/).

Composer and npm are not required to build this customization:

- Composer installs development dependencies used for tests, code style, and static analysis.
- npm is needed only when regenerating the workflow's icons.

The repository's [development instructions](https://github.com/TarjinderSingh/alfred-github-workflow/blob/main/AGENTS.md) describe those optional development steps.

## Disable upstream automatic updates

The workflow's update command is hard-coded to download releases from `gharlan/alfred-github-workflow`, not from this fork. After installing the custom build, run the following command through Alfred:

```text
gh > deactivate autoupdate
```

Do not run `gh > update`. It downloads and installs the latest upstream release, which would replace the custom build. The implementation is visible in [`src/action.php`](https://github.com/TarjinderSingh/alfred-github-workflow/blob/fix/copy-ssh-clone-url/src/action.php#L91-L105).

## Keep the fork synchronized

Keep `main` pristine and continue building from `fix/copy-ssh-clone-url`.

Configure the upstream remote once:

```bash
git remote add upstream https://github.com/gharlan/alfred-github-workflow.git
git remote -v
```

Whenever upstream changes, fast-forward `main`, merge it into the customization branch, rebuild, and reinstall:

```bash
git fetch upstream

git switch main
git merge --ff-only upstream/main
git push origin main

git switch fix/copy-ssh-clone-url
git merge main
git push origin fix/copy-ssh-clone-url

./bin/build
open ./github.alfredworkflow
```

This merge-based approach preserves the history of the published customization branch. Merge conflicts should occur only if upstream changes the same areas as the customization, currently `src/action.php` and `src/search.php`.

As an alternative, use GitHub's **Sync fork** button to update `main`, then merge `main` into `fix/copy-ssh-clone-url` locally. [GitHub's fork-sync documentation](https://docs.github.com/en/pull-requests/how-tos/work-with-forks/syncing-a-fork) covers both the web and command-line approaches.
