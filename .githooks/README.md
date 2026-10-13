# Git hooks

`pre-commit` scans the added lines of the staged diff and blocks the commit when it finds a likely secret:
private key headers, AWS access key IDs, GitHub and Slack tokens, live payment keys (`sk_live_`, `pk_live_`),
long key-like values assigned to names containing `SECRET`, `TOKEN`, `PASSWORD`, `API_KEY` or `PRIVATE_KEY`,
and database URLs that carry a real password. It also refuses files named `.env.local`, `.env.*.local`,
`*.pem`, `*.key` and `id_rsa*`. Obvious placeholders (`change-me`, `example`, `${VAR}`, `%env(...)%`) pass.

Enable it once per clone (hooks are not active until you do): `git config core.hooksPath .githooks`.
On macOS and Linux the script must be executable: `chmod +x .githooks/pre-commit`.
To check every file already in the index, run `sh .githooks/pre-commit --all`.

False positive: put the marker `secret-scan:allow` on the reported line (in a comment) and stage it again.

This hook is a first line of defence. It can be skipped and it knows only a few patterns, so it is not a
substitute for server-side secret scanning and push protection on the hosting platform.
