# depquiet-fixpr-test

Test project for [depquiet](https://github.com/samuelrojas-dev/depquiet)'s `fix-pr`.

`requests==2.31.0` and `botocore==1.29.0` are pinned on purpose. botocore 1.29.0 requires
`urllib3<1.27`, so the urllib3 fixes need botocore to move too: the case Dependabot reports
as `update_not_possible` in Python projects (dependabot-core#16337).

Run **Actions → depquiet fixes → Run workflow**: depquiet opens one pull request with
every fix that uv's resolver confirms installs together.
