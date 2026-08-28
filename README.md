# CSP初赛

这是一个面向 CSP-S/NOIP 初赛复习的自制模拟卷仓库，主要内容是 2026 年提高级模拟卷及其参考答案。仓库显示名为“CSP初赛”，GitHub 路径为 `tangyixiao/CSP-ChuSai`。

> 重要说明：本仓库不是任何官方机构发布的试题，也不代表任何官方机构。题面中的 `C5P`、`€€£` 等仅用于自制模拟卷的视觉标识；题目、代码和解析由本仓库重新组织。

## 核心文件

- `c5p-s-2026.tex`：模拟卷题面，43 个小题，满分 100 分。
- `c5p-s-2026-answer.tex`：逐题答案与解析，含题目目标、关键变量、不变量、推导、反例、复杂度和样例追踪。
- `c5p-s-2026.pdf`：题面 PDF。
- `c5p-s-2026-answer.pdf`：答案与解析 PDF。
- `kaodian-matrix.md`：题号、分值、考点和难度矩阵。
- `NOI_Syllabus_Edition_2025.pdf`：命题边界参考考纲。
- `feedback/review-2026-08-27.jpg`：本次重构使用的反馈图片存档。
- `csp-j初赛真题/`、`csp-s初赛真题/`：现有往届资料归档。

## 分值结构

| 部分 | 题号 | 分值 |
|---|---:|---:|
| 单项选择 | 1--15 | 30 |
| 阅读程序（归并排序、单调栈、树的直径） | 16--33 | 40 |
| 完善程序（第 K 小和、Kruskal 最小生成树） | 34--43 | 30 |
| **合计** | **1--43** | **100** |

## 编译

在安装 XeLaTeX、CTEX 和 TikZ 的环境中执行：

```powershell
xelatex -interaction=nonstopmode -halt-on-error c5p-s-2026.tex
xelatex -interaction=nonstopmode -halt-on-error c5p-s-2026.tex
xelatex -interaction=nonstopmode -halt-on-error c5p-s-2026-answer.tex
xelatex -interaction=nonstopmode -halt-on-error c5p-s-2026-answer.tex
```

生成的 `.aux`、`.log`、`.out`、`.toc`、`.synctex.gz` 和 `.cph-ng` 等构建产物不会纳入版本控制。

## 资料与权利

考纲、往届试题、图片和链接资料按原文件保留，相关著作权和使用条件归原作者或发布方所有。本仓库只对自制题面、答案解析和整理结构负责；外部题源仅作命题结构与知识点参照，不将外部材料宣称为本仓库原创。

反馈图仅作为项目审阅记录保存，不作为题面内容或官方标识使用。
