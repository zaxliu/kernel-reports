# kernel-reports

GitHub Pages site hosting evaluation reports for the
[better_kernels](https://github.com/zaxliu/better_kernels) project
(agent-loop GPU kernel optimization, KernelBench L1/L2/L3 on 8× RTX 5090).

Live site: **https://zaxliu.github.io/kernel-reports/**

## Structure

```
.
├── index.html              ← landing page (links to per-level reports)
├── l1/index.html           ← L1 full report (100 problems × 3 frameworks)
├── l2/                     ← (placeholder)
├── l3/                     ← (placeholder)
└── .github/workflows/
    └── deploy.yml          ← auto-deploys on push to main
```

## Where this repo lives on disk

This is a **separate public repo** physically located inside the parent
`better_kernels` working tree:

```
~/Documents/code/better_kernels/
├── .git/                ← better_kernels (separate, NOT this repo)
├── .gitignore           ← contains `/reports/` so parent ignores us
├── experiments/...
└── reports/             ← ★ THIS repo (kernel-reports), own .git/
    ├── .git/
    ├── index.html
    └── l1/index.html
```

Use `cd reports && git ...` to work on this repo; the parent `better_kernels`
git does not see anything inside.

## How reports are produced

Reports are built locally inside the `better_kernels` repo:

```
cd better_kernels/experiments/L1_full_report
python3 build_html.py
```

Then synced into this repo with the publish script:

```
cd better_kernels/experiments
bash publish_reports.sh           # syncs all known reports
bash publish_reports.sh l1        # only L1
```

The script copies the built HTML into the right subdir of `kernel-reports`,
runs `git add` / `commit` / `push`, and the deploy workflow takes over.

## Deploy

`.github/workflows/deploy.yml` runs on every push to `main` and uses the
official `actions/deploy-pages` action. No build step on CI — static files
are uploaded as-is.

First-time setup (one-time, in GitHub web UI):
**Settings → Pages → Source = GitHub Actions**.
