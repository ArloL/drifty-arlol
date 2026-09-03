# drifty-arlol

`drifty.pkl` is the desired state of every repository under the ArloL GitHub
account, and nothing applies it. A merge to main changes this file only; the
settings on GitHub change when someone runs [drifty][] from a clone of this
repo.

[drifty]: https://github.com/ArloL/drifty

## Running drifty

`drifty` on its own reports drift and exits non-zero if it found any, `drifty
--fix` makes GitHub match the config. Both read `drifty.pkl` from the working
directory and need `DRIFTY_GITHUB_TOKEN` and `DRIFTY_GITHUB_SECRETS` in the
environment, which `mise.local.toml` supplies.

Repositories reported as `UNKNOWN` exist under the account but are absent from
the config, and drifty leaves them alone.

## Secrets

`mise.local.toml` holds every secret value and is gitignored, so a fresh clone
cannot run `--fix`: it checks the whole config up front and exits listing each
key it could not find. Keys are `<repo>-<SECRET>` for `actionsSecrets` and
`<repo>-<environment>-<SECRET>` for environment secrets.

`drifty-state.json`, also gitignored, records when drifty last pushed each
secret and a salted hash of the value it pushed. That is how a value edited in
`mise.local.toml` and a secret rotated in the web UI both show up as drift.
Without the file every configured secret reads as drift and `--fix` pushes all
of them again.

## Templates and required checks

A repository with no workflows takes `noRulesRepo`. `defaultRepo` and the
templates derived from it require the check-actions, codeql-analysis and CodeQL
status checks on main, and GitHub holds a pull request until every required
check reports — a check whose workflow does not exist never reports, so the pull
request never merges.

Require a status check only once its workflow is merged and green on main, for
the same reason.

## Editing pkl in VS Code

`.vscode/settings.json` formats `.pkl` files on save, which does nothing until
the Pkl extension is installed. VS Code will not prompt for it either:
`.vscode/extensions.json` can only recommend extensions from the marketplace,
and this one ships as a `.vsix` on [its releases page][pkl-vscode].

Download the `.vsix`, then:

```
code --install-extension ~/Downloads/pkl-vscode-<version>.vsix
```

The language server needs Java 22+ on `PATH` or in `JAVA_HOME`. Point
`pkl.lsp.java.path` at a specific executable when neither has one new enough.

[pkl-vscode]: https://github.com/apple/pkl-vscode/releases/latest
