# 交接报告 · report.md

> 只由 DS 写；只给结论，不贴整屏 log。

## R2 · 六行对账（HEAD b6e5fd5，未改任何代码）

1. **git show 1993ac2 -- src/lock-store.js**
```diff
--- a/src/lock-store.js
+++ b/src/lock-store.js
@@ -37,8 +37,8 @@ function writeDoc(storage, doc) {
 export function readLock(quizId, storage) {
   const doc = readDoc(storage);
   const it = doc.items[quizId];
-  if (!it) return { locked: false, kp: null, streak: 0, at: null };
-  return { locked: it.locked === true, kp: it.kp || null, streak: it.streak || 0, at: it.at || null };
+  if (!it) return { locked: false, kp: null, streak: 0, at: null, corrected: false };
+  return { locked: it.locked === true, kp: it.kp || null, streak: it.streak || 0, at: it.at || null, corrected: it.corrected === true };
 }
```

2. **hasLock() 返回语义**：没变。仍为 `hasOwnProperty(doc.items, quizId)`，返回布尔（第 45-48 行未动）。

3. **readLock() 无记录时**：仍返 `{locked:false, kp:null, streak:0, at:null}`，**只多一个 `corrected:false` 字段**；不返回 undefined，语义未破。

4. **build-report/p3-baseline-anchor.json**：**不存在**。→ 没有 FINAL sha；窗口尚未收口。

5. **drift 的 state 字段**：**P3-OPEN**（`build-report/anchor-expected-drift.json`：baseFrom=88CAD27A…/8135390，closeAt=build-report/p3-baseline-anchor.json 落地，window=[P3-2, P3-7]）。

6. **check-anchor 现在**：**窗口 `--card` 归因**（state=P3-OPEN 时走 WINDOW 模式：确定性构建 + 变化必须用 `--card` 归因；窗口关闭后才恢复等值硬判）。当前窗口哈希 `5B9953FC…BD67DDAC / 8144036`，最近一跳 card=`P3-UI-fix`。

### 结论
- 冻结区守卫与 lock-store 语义**未破**：只新增一个字段 `corrected`，读路径兼容。
- 锚状态：**窗口开着**，没有新基线；P3-7 落地 `p3-baseline-anchor.json` 才关窗。
- 本轮**未改 extras.js、未建三态 test**。
