# CLAUDE.md — kernel-reports

## 仓库定位

这是一个**只承载静态 HTML 报告**的公开仓,通过 GitHub Pages 对外发布
[better_kernels](../) 项目的评测结果。不放任何源码 / 数据 / 工具。

- **Live site**: https://zaxliu.github.io/kernel-reports/
- **Pages source**: GitHub Actions (workflow 在 `.github/workflows/deploy.yml`)
- **物理位置**: `~/Documents/code/better_kernels/reports/` (是 better_kernels 工作树
  下的独立 git 仓,被父仓 `.gitignore` 屏蔽,互不干扰)

## 严格约束

- **不要手改 `l1/index.html` 等报告 HTML**。它们是 `better_kernels/experiments/
  <Lx>_full_report/build_html.py` 生成的产物,手改会在下次 `publish_reports.sh`
  运行时被覆盖。要改报告内容,去改 `better_kernels` 仓里的 build_html.py / markdown。
- **不要提交大文件**。kernel 代码已经嵌入 HTML payload(JSON 字段),不要额外放
  `.json` / `.py` / 数据集 到本仓。
- **不要改 `.nojekyll`**。空文件,告诉 Pages 跳过 Jekyll 处理 (否则下划线开头的
  目录会被忽略)。
- **不要切走 main 分支**。Pages workflow 只跟 `main`。

## 目录布局

```
.
├── index.html              ← landing 页 (L1/L2/L3 入口卡)
├── l1/index.html           ← L1 完整报告
├── l2/                     ← (placeholder, L2 完成后填)
├── l3/                     ← (placeholder)
├── .nojekyll
├── README.md
├── CLAUDE.md               ← 本文件
└── .github/workflows/
    └── deploy.yml          ← 每次 push main 触发 actions/deploy-pages
```

## 改动入口

| 想做什么 | 改哪 | 怎样落到这里 |
|---------|------|-------------|
| 更新 L1 报告内容 | `../experiments/L1_full_report/{L1_FULL.md, build_html.py}` | `cd ../experiments && bash publish_reports.sh l1` |
| 加 L2 报告 | `../experiments/L2_full_report/build_html.py` 产出 `L2_FULL.html` | `bash publish_reports.sh l2` |
| 改 landing 页 | 直接编辑 `index.html` | `git add index.html && git commit && git push` |
| 改部署逻辑 | `.github/workflows/deploy.yml` | push 后 GH Actions 用新 workflow |

## 发布流程

1. 在父仓 `better_kernels/experiments/<Lx>_full_report/` 跑 `python3 build_html.py`。
2. 在父仓 `experiments/` 跑 `bash publish_reports.sh [l1|l2|...]`,脚本会:
   - 把生成的 HTML 拷成 `<lx>/index.html`
   - `git add` / `commit` / `push origin main`
3. GH Actions 自动跑 `deploy.yml`,30s 内 https://zaxliu.github.io/kernel-reports/
   刷新。

如果 publish 脚本检测到没有改动,会跳过 commit,不会产生空提交。

## 常见排查

- **Pages 不刷新** → 看 `gh run list -L 3`,失败的话点开看 step 输出。
- **Actions 跑了但页面是旧的** → 浏览器强刷 (Cmd+Shift+R);Pages CDN 有几分钟
  缓存。
- **TLS handshake timeout (git push / gh api)** → 父仓 CLAUDE.md 已记: 挂
  `127.0.0.1:7890` 代理重试。
- **landing 页链接 404** → 该 `<lx>/index.html` 还没生成或 publish 失败;
  跑 `ls l1/ l2/ l3/` 确认。

## 不属于这里的东西

- 评测原始数据 (`verify_all_l1.json` 等) → 留在 `better_kernels/experiments/`,
  不要复制进来。
- build_html.py / publish_reports.sh → 工具属于父仓。
- markdown 源稿 (`L1_FULL.md`) → 源稿留父仓,只 HTML 渲染产物落这里。
