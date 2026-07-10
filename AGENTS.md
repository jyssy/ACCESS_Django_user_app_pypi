# ACCESS Django User Admin library agent instructions

## Purpose and boundaries

This repository is the `access_django_user_admin` shared library: a reusable Django app,
published to PyPI, that queries the ACCESS Allocations User API and lets an operator
select a result for inclusion in a project's users and social-account tables. It is a
producer: its consumers are the ACCESS-CI Django applications that install and import it.

Library code and packaging belong here. Application-specific behavior, deployment, and
configuration belong to the consuming applications, not to this repository.

## Sources of truth and coupling

- `README.md` describes the intended import-and-select usage.
- `pyproject.toml` and `setup.py` define the package name (`access_django_user_admin`),
  version, and Python support; `MANIFEST.in` declares packaged templates, template tags,
  and static assets. The version is the release contract.
- `src/access_django_user_admin/` holds the public interface: views, templates, template
  tags, and static assets that consumers import and render.
- `tests/` covers that interface. `requirements.txt`, `dist/`, and `.idea/` are build,
  artifact, and editor concerns.
- Consumers include `../Operations_PortalCMS_Django`, `../service-index-uv-sand`
  (`Operations_ServiceIndex_Django`), and `../Operations_Dashboard_Django`, which pin a
  specific released version. A change to the public views, templates, template tags, or
  version is a producer/consumer boundary. See `multi_agent_plan.md` at the workspace root
  for the wiring.

## Safe inspection and validation

Always begin at the Git root:

```bash
git status --short --untracked-files=all
git diff --check
```

Use the narrowest relevant checks:

```bash
python -m pytest tests/<affected_test>
python -m build   # only to verify packaging locally; do not upload
```

Do not contact the live Allocations User API with real credentials during validation.
Record a missing API key as a skipped check rather than inventing one.

## Safety and change control

- A human must approve version bumps, tags, releases, and any publish to PyPI. Never run
  `twine upload` or otherwise publish from an agent.
- A change to public views, template names, template-tag names, or rendered context is a
  breaking change for consumers; freeze the interface and get consumer-impact review
  before releasing.
- Never commit an API key, token, or credential. Do not hardcode the Allocations API key
  or its path.
- Do not commit build artifacts (`dist/`, `*.egg-info`) or the local virtual environment.

## Worktree and multi-agent rules

Inspect the full dirty state before editing and preserve unrelated tracked and untracked
files. Never reset, clean, or overwrite user work. Use one writer per repository or
isolated worktree. Because this is a shared producer, a task touching the public interface
must have one designated writer, a frozen public contract, and read-only consumer-impact
review across the importing applications before a new version is released.

Every delegated task must state the repository root and base commit, the role and allowed
write paths, prohibited paths and external actions, the frozen public interface and
dependent repositories, the required validation checks, and the expected handoff. The
handoff must list files changed, the final diff summary, checks run and their results,
failures or skipped checks, assumptions, and unresolved risks.

## Wiring

See `multi_agent_plan.md` at the workspace root. This is a producer library; consumers
include `Operations_PortalCMS_Django`, `service-index-uv-sand`, and
`Operations_Dashboard_Django`.
