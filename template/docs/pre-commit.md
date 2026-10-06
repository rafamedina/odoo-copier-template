# Pre-commit

Every commit runs a set of checks first. If one fails, the commit stops. Fix the
problem, stage the files again, commit again.

Some hooks fix files for you. They still fail on the first run. Stage the changes, then
commit again.

## Setup

Run this once after cloning.

```bash
uv sync
uv run pre-commit install --install-hooks
```

## When hooks run

Most hooks run on `git commit`. They only look at staged files.

commitlint runs right after you write the commit message.

## Odoo checks

**en.po files cannot exist** blocks `i18n/en.po`. Odoo already uses the source strings
for English.

**oca-checks-odoo-module** checks XML, CSV, manifest files. It catches duplicated record
IDs. It flags tags removed in Odoo 18, like `<data>` without attributes. It fixes what
it can.

**oca-checks-po** checks translation files. Placeholders in `msgstr` must match the ones
in `msgid`.

## Formatting

**prettier** formats XML, YAML, JSON, Markdown, CSS. Settings live in
`prettier.config.cjs`.

**eslint** lints JavaScript. It skips `commitlint.config.js` because that file runs in
Node, not in Odoo.

**ruff** lints Python. It sorts imports with separate Odoo sections. It fixes what it
can. Settings live in `.ruff.toml`.

**ruff-format** formats Python.

## File checks

**trailing-whitespace** removes spaces at the end of lines.

**end-of-file-fixer** leaves exactly one newline at the end of each file.

**debug-statements** blocks leftover `pdb` imports or `breakpoint()` calls.

**check-case-conflict** blocks file names that differ only in case. Those break on macOS
or Windows.

**check-docstring-first** makes sure the module docstring comes before any code.

**check-executables-have-shebangs** requires a `#!` line in every executable file.

**check-merge-conflict** blocks leftover `<<<<<<<` markers.

**check-symlinks** blocks broken symlinks.

**check-xml** fails on invalid XML.

**mixed-line-ending** converts every line ending to LF.

**check-yaml** fails on invalid YAML.

**check-toml** fails on invalid TOML.

**check-added-large-files** blocks files over 1 MB. Database dumps don't belong in git.

**detect-private-key** blocks SSH or TLS private keys.

## Pylint

**pylint with optional checks** runs everything in `.pylintrc`. It only prints warnings.
It never blocks a commit. A module without a README shows up here.

**pylint_odoo** runs the rules in `.pylintrc-mandatory`. These do block the commit. SQL
injection is one of them. A missing `super()` call is another. So is a license or author
the repo doesn't allow.

## Secrets

**detect-secrets** looks for passwords, tokens, API keys. Known findings live in
`.secrets.baseline`. Anything new blocks the commit.

If it's a false positive, update the baseline.

```bash
uvx detect-secrets==1.5.0 scan --baseline .secrets.baseline
```

## Commit message

**commitlint** checks the message against Conventional Commits. It uses the default
rules, so types like `feat`, `fix`, `docs`, `chore` all work.

```text
feat(shifts): add weekly summary view
fix: handle empty attendance list
```
