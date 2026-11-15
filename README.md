# depquiet fix-pr demo

A small project with a vulnerable dependency that Dependabot cannot fix, and the pull
requests [depquiet](https://github.com/samuelrojas-dev/depquiet) opened to fix it.

## The problem

`requests==2.31.0` and `botocore==1.29.0` are pinned on purpose. botocore 1.29.0 requires
`urllib3<1.27`, so every urllib3 fix (2.5.0 and later) needs botocore to move first.
Dependabot does not raise a parent package to fix a transitive one in Python projects
([dependabot-core#16337](https://github.com/dependabot/dependabot-core/issues/16337)): it
reports that it cannot update the dependency to a non-vulnerable version, and the alerts
stay open.

`pip-audit` reports 10 alerts here: 3 in requests, 7 in urllib3.

## What depquiet did

The [depquiet fixes](.github/workflows/depquiet-fixes.yml) workflow runs the depquiet
GitHub Action with `fix-pr: "true"`. depquiet worked out that botocore has to move to
1.44.3 for urllib3 to reach 2.8.0, checked with uv's resolver that every fix installs
together, and opened one pull request:

- **uv project** (this folder): [pull request #1](../../pull/1)
- **Poetry project** ([`poetry-app/`](poetry-app)), verified with Poetry's resolver:
  [pull request #2](../../pull/2)

Each changes only the requirements and the lockfile. A scan after the change finds 0
alerts. depquiet checks that the versions resolve, not that the code works with them:
urllib3 2 is a major version, so a real project would let its tests decide.

## Try it on your project

Copy [`.github/workflows/depquiet-fixes.yml`](.github/workflows/depquiet-fixes.yml), then
in **Settings → Actions → General** allow GitHub Actions to create pull requests. Run it
from the **Actions** tab. On your machine, `depquiet --fix-plan` shows the same plan
without changing anything:

```bash
pip install depquiet pip-audit
depquiet --fix-plan
```

Docs: [depquiet README](https://github.com/samuelrojas-dev/depquiet#one-verified-change-for-all-the-fixes---fix-plan).
