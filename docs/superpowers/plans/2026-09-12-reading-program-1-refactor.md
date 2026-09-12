# 阅读程序（1）重构 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在保留原题考察内容和题面风格的前提下，将阅读程序（1）改为中等复杂度、无明显算法命名提示的指针实现，并同步答案、图表、行号、矩阵和 PDF。

**Architecture:** 使用动态 `char* data` 保存原字符串，使用动态 `int* mark` 保存回退信息；`readAt`、`fetch`、`settle` 三个辅助函数分别负责指针取值、读取历史回退值和完成一次位置调整。主函数保留原来的逐位置处理与沿回退链累加结构，因此语义、复杂度和题目答案不变。

**Tech Stack:** C++14 code listing, XeLaTeX, TikZ, Poppler (`pdftotext`, `pdftoppm`, `pdfinfo`), GitHub Actions.

**Spec:** `docs/superpowers/specs/2026-09-12-reading-program-1-design.md`

## Global Constraints

- 题面代码不得出现 `pi`、`prefix`、`KMP`、`failure`、`next` 等明显提示性标识。
- 保留原题 16--20 的题型、选项、考察点和答案。
- 题面与答案中的程序行号必须以新代码实际行号为准。
- 只修改合并版题面、答案、PDF、考点矩阵及本设计/计划文件。
- 保留工作区中未批准的 `c5p-s-2026-revised-answer.tex` 和 `c5p-s-2026-revised.tex` 修改，不暂存、不提交。

---

### Task 1: Replace the reading program and synchronize question references

**Files:**
- Modify: `c5p-s-2026-merged.tex:224-252`

**Interfaces:**
- Produces the question code listing and Q16--20 references used by the answer document.

- [x] **Step 1: Replace the code listing with the approved moderate-complexity implementation**

Use these exact identifiers and operations:

```cpp
const int N = 2e5 + 5;
char *data;
int *mark;
int size;

char readAt(int pos) {
    return *(data + pos);
}

int fetch(int len) {
    return len == 0 ? 0 : *(mark + len - 1);
}

int settle(int pos, int candidate) {
    while (candidate && readAt(pos) != readAt(candidate)) {
        candidate = fetch(candidate);
    }
    return candidate + (readAt(pos) == readAt(candidate));
}

int main() {
    data = new char[N]{};
    mark = new int[N]{};
    cin >> data;
    size = strlen(data);

    for (int i = 1; i < size; ++i) {
        *(mark + i) = settle(i, fetch(i));
    }

    int answer = 0;
    for (int len = size; len > 0; len = fetch(len)) {
        answer += len;
    }

    cout << answer << '\\n';
    delete[] mark;
    delete[] data;
    return 0;
}
```

- [x] **Step 2: Recount the listing lines from the generated source**

Use the actual listing line numbers to update Q16--20. Q16 should refer to the new `mark` entry at index 4, Q17 to lines 34--35 of the final accumulation loop, Q18 to line 17 in `settle`, and Q19--20 to the new output and complexity behavior. Do not carry over the previous `pi` wording or old line numbers.

- [x] **Step 3: Run a source-level terminology audit**

Run:

```powershell
rg -n 'pi|prefix|KMP|failure|next' c5p-s-2026-merged.tex
```

Expected: no matches attributable to the reading program (1) code or its question wording.

### Task 2: Synchronize answer explanations, diagram, and matrix

**Files:**
- Modify: `c5p-s-2026-merged-answer.tex:64-203`
- Modify: `kaodian-matrix-final.md:11-43`

**Interfaces:**
- Consumes the final line numbers and identifiers from Task 1.
- Produces answer explanations and matrix terminology consistent with the new listing.

- [x] **Step 1: Replace answer references and identifiers**

Use `mark`, `fetch`, and the actual generated line numbers in Q16--20 explanations. Explain the same boundary-record behavior in prose, but do not reintroduce `pi` in the code-facing explanation or diagram.

- [x] **Step 2: Rename the answer diagram labels**

Use neutral labels such as “长度回退链” and `mark`-free length transitions. Preserve the existing chart style and size.

- [x] **Step 3: Verify answer and matrix consistency**

Run:

```powershell
rg -n 'pi|prefix|KMP|failure|next|仿射|区间乘加线段树' c5p-s-2026-merged-answer.tex kaodian-matrix-final.md
```

Expected: no forbidden algorithm hints in the revised reading-program section, no old “仿射” title, and the segment-tree name is consistently “区间乘加线段树”.

### Task 3: Build and inspect the final PDFs

**Files:**
- Generate: `c5p-s-2026-merged.pdf`
- Generate: `c5p-s-2026-merged-answer.pdf`
- Inspect: `tmp/detail_answer_build/*.log`, extracted text, and rendered PNG pages

**Interfaces:**
- Consumes the synchronized TeX and matrix files from Tasks 1--2.
- Produces the final PDFs for the pull request.

- [x] **Step 1: Compile both documents twice with XeLaTeX**

```powershell
xelatex -interaction=nonstopmode -halt-on-error -file-line-error -jobname=c5p-s-2026-merged 'D:\\Study\\信奥\\CSP\\c5p-s-2026-merged.tex'
xelatex -interaction=nonstopmode -halt-on-error -file-line-error -jobname=c5p-s-2026-merged 'D:\\Study\\信奥\\CSP\\c5p-s-2026-merged.tex'
xelatex -interaction=nonstopmode -halt-on-error -file-line-error -jobname=c5p-s-2026-merged-answer 'D:\\Study\\信奥\\CSP\\c5p-s-2026-merged-answer.tex'
xelatex -interaction=nonstopmode -halt-on-error -file-line-error -jobname=c5p-s-2026-merged-answer 'D:\\Study\\信奥\\CSP\\c5p-s-2026-merged-answer.tex'
```

- [x] **Step 2: Scan logs and extracted text**

Check that the question PDF has 17 pages, the answer PDF has 6 pages, and logs contain no LaTeX errors, undefined references, overfull boxes, underfull boxes, emergency stops, or missing characters. Confirm the extracted question text contains the neutral pointer implementation and the updated line references.

- [x] **Step 3: Render and inspect representative pages**

Render both PDFs with `pdftoppm`. Inspect the reading-program code page, the page containing Q16--20, the answer page containing the new diagram and Q16--20, the line-segment-tree page, and the title/scoring page.

### Task 4: Commit, PR, and remote verification

**Files:**
- Stage only: `c5p-s-2026-merged.tex`, `c5p-s-2026-merged-answer.tex`, `c5p-s-2026-merged.pdf`, `c5p-s-2026-merged-answer.pdf`, `kaodian-matrix-final.md`, and the two plan/spec files.

**Interfaces:**
- Produces a scoped Git commit and a pull request based on the current `main`.

- [ ] **Step 1: Check the staged scope**

```powershell
git diff --cached --check
git diff --cached --name-only
git diff --cached --stat
```

The two `c5p-s-2026-revised*.tex` files must not appear.

- [ ] **Step 2: Commit and push the scoped change**

```powershell
git commit -m 'docs: refactor reading program one'
git push -u origin codex/refactor-reading-program-1
```

- [ ] **Step 3: Wait for CI and merge only after both LaTeX checks pass**

Use `gh pr checks <number> --watch --interval 10`, merge the PR with `gh pr merge <number> --merge --delete-branch`, then verify the merge commit and the equality of local `main` and `origin/main` SHA values.

- [ ] **Step 4: Confirm preservation of unrelated work**

After returning to `main`, run `git status --short --branch`. The only remaining local modifications should be the pre-existing `c5p-s-2026-revised-answer.tex` and `c5p-s-2026-revised.tex` files.
