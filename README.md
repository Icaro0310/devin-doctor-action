# devin-doctor-action

GitHub Action that gates a runner's Devin environment with
[`devin-doctor`](https://pypi.org/project/devin-doctor/) — the
environment-health package from the devin-* ecosystem.

This is a **post-install gate**, not an installer and not a repo scan:
it asserts that a Devin install *already present* on the runner is
healthy. Install Devin first via `devin-devkit` or a setup step.

## The two distinct failures

| Situation | Message | Meaning |
|---|---|---|
| no Devin data dir found | `NOT INSTALLED` | violated precondition — nothing to health-check |
| install present, FAIL findings | `UNHEALTHY` | what this action exists to catch |

Conflating the two would hide a broken install behind "not installed".

## Usage

```yaml
- uses: Icaro0310/devin-doctor-action@v1
```

Typical post-setup assertion:

```yaml
- name: Install Devin toolchain
  run: <your setup>
- uses: Icaro0310/devin-doctor-action@v1
  with:
    fail-on-warn: "false"
```

On a self-hosted runner with a persistent install, plain
`uses:` checks the standard platform locations.

## Inputs / outputs

- `data-dir`, `config-dir` — optional overrides for the checked dirs.
- `version` — pip specifier (default `devin-doctor>=0.1,<0.2`).
- `fail-on-warn` — WARN findings also fail the job (default `false`).
- outputs: `overall` (`PASS`/`WARN`/`FAIL`), `not_installed`,
  `failures` (FAIL count).

## Notes

- Read-only by design — it inspects environment state and never
  modifies it; it never touches the checked-out repo (no `paths`).
- The satellite repo is only the Action distribution vehicle; all
  health logic lives in
  [`devin-doctor`](https://github.com/Icaro0310/devin-explore/tree/main/packages/doctor).
