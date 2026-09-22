# 依赖图分析实验

> 对象：`video-auto-cut` ｜ 日期：2026-09-22

实验的做法是先由脚本把代码库扫成依赖关系，再由人逐个节点追问「它运行时需要什么」；不只看结构，也看结构背后的语义。本目录归档这一轮产物：一处图上的读数，一条由此暴露的架构问题，以及围绕它的成因与风险梳理。

## 文件

- `dependency-graph.html`：`video-auto-cut` 的模块依赖关系图，22 节点 / 40 边，是本轮全部读数的共同来源。
- `architecture-review-renderer-boundary.md`：据图与源码实证写成的架构评审，主体结论是 `worker.mjs` 实为第二台业务机。
- `server-worker-out-edges.html`：`server` 与 `worker` 的直接出边对比，对应评审 §1.1，内部出边逐条相等，差异仅落在 Web 层。
- `server-worker-runtime-requirements.html`：把图上的节点落回真实运行环境，对应评审 §1.2，图看不出环境需求与产物落盘位置。
- `renderer-two-layer-causes.html`：两层成因的锁扣，一个变量加一个文件，对应评审 §2.1 与 §2.3。
- `renderer-core-crux.html`：核心症结一图，渲染机重的不是渲染，是它顺手把 `base` 也干了。
- `dependency-risk-matrix.html`：依赖风险矩阵，12 项风险按影响与可能性排布，红色为立即修。

图与评审同源：前者是结构提取的结果，后者是对同一份结构的语义追问，两者不应分别维护。

## 结论

渲染机之所以重，是因为分派代码用 `kind` 一个变量同时回答了「到哪个阶段」与「在哪台机器」，又由 `proxy.mp4` 只认本机路径把两种环境钉死在一起。
