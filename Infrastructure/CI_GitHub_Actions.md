# CI — GitHub Actions

The backend's continuous-integration pipeline, `.github/workflows/ci.yml`. The frontend has no CI workflow in the repo.

---

## Overview

Triggers:

- every `pull_request`
- `push` to `main` or to `feature/github-actions`. The second branch is temporary; drop it once CI is proven.

`concurrency: ci-${{ github.ref }}` with `cancel-in-progress` means a newer run cancels an older one.

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart LR
    trig["<b>PR / push to main</b>"]
    subgraph lint ["job: lint"]
        l1["checkout + Python 3.12<br/>pip cache"] --> l2["ruff check ."] --> l3["ruff format --check ."]
    end
    subgraph test ["job: test"]
        pg[("<b>service</b><br/>postgres:16")]
        t1["checkout + Python 3.12"] --> t2["makemigrations --check --dry-run"] --> t3["pytest"]
        t3 --> pg
    end
    trig --> lint
    trig --> test

    style trig fill:#08427B,color:#fff,stroke:#073B6F
    style l1 fill:#438DD5,color:#fff,stroke:#3579B8
    style l2 fill:#438DD5,color:#fff,stroke:#3579B8
    style l3 fill:#438DD5,color:#fff,stroke:#3579B8
    style t1 fill:#438DD5,color:#fff,stroke:#3579B8
    style t2 fill:#438DD5,color:#fff,stroke:#3579B8
    style t3 fill:#438DD5,color:#fff,stroke:#3579B8
    style pg fill:#999,color:#fff,stroke:#888
    style lint fill:#E8E8E8,color:#333,stroke:#BBB
    style test fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = trigger
- **Blue** = steps
- **Gray** = service container
- **Light gray** = job boundary

---

## Jobs

| Job | Steps | Notes |
|-----|-------|-------|
| `lint` | ruff check, ruff format --check | No DB needed |
| `test` | migrations check, pytest | `postgres:16` service with a `pg_isready` health check. Env values are CI-only throwaways (`DJANGO_SECRET_KEY: ci-only-insecure-key`, `vip_ci` DB/user). They are not secrets |

The **migrations check** fails the build if a model change was committed without its migration.

---

## Future Hooks

A merge to `main` is the natural trigger for auto-updating this vault. See the `vip-docs-sync` skill and [[Project_Structure]].

---

## Related Notes

- [[Testing]]
- [[Configuration]]
- [[GitHub_Actions_CI]]
