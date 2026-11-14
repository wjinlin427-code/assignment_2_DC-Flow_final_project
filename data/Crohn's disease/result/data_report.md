# Crohn's disease 靶点环肽生成报告

## 结果概览

使用 DCFlow checkpoint 从头生成 HT（head-to-tail）环肽。每个 PDB 靶点独立生成 64 条候选（2 轮 × 32 条；200 个时间点），经过端基闭环筛选后按界面几何启发式分数排序，输出排名前 10 的复合物 PDB 和肽链 PDB。

## 设计框架与生成流程

本流程采用“靶点结构准备—界面定义—条件生成—结构筛选—候选排序—结果归档”的计算设计框架。目标是在给定蛋白界面附近，由 DCFlow 生成新的头尾相连（head-to-tail，HT）环肽主链与氨基酸序列，再根据结构几何性质筛出可用于后续评估的候选。生成序列不是对原共晶肽序列的复制，也没有将原共晶肽构象用作生成模板。

1. **靶点及链识别**：从输入 PDB 中识别蛋白链。6UIB 和 8UUI 以 IL-23 的 A、B 链作为受体；9P95 以 integrin α4β7 的 A、B 链作为受体。相应共晶界面链只用于定位参考结合区域；6UIB、8UUI 的共晶肽长度分别作为生成长度参考，9P95 则采用 12 aa 的设计长度。
2. **受体与口袋准备**：提取受体完整结构，并围绕参考界面构建局部生成口袋：保留距参考配体重原子 12 Å 内、具有完整 N/CA/C/O 主链的受体残基。参考配体相关重原子坐标用于确定生成区域中心；原共晶配体不作为生成模板或输出肽序列。
3. **条件式从头生成**：对每个靶点独立运行预训练 DCFlow 模型。模型在受体口袋条件下，从随机状态逐步演化肽链骨架、侧链角度与序列；同时使用 ProteinMPNN 先验辅助序列预测。每个靶点生成两批、每批 32 个候选，采样步数为 200。不同靶点及重复批次使用独立随机种子。
4. **环肽几何筛选**：对候选结构检查头尾闭环端基距离，即首残基 N 与末残基 C 的距离。仅保留 C–N 距离为 1.2–1.6 Å 的候选，以排除闭环几何明显不合理的结构；最终复合物 PDB 另写入端基 `CONECT` 记录，显式标记头尾连接。
5. **界面几何打分与靶点内排序**：计算受体与肽链重原子间距离，统计最小距离不超过 5 Å 的受体—肽链残基对数，以及最小距离小于 2 Å 的严重碰撞残基对数。采用启发式分数 `S = 接触残基对数 − 5 × 严重碰撞残基对数`，在每个靶点各自的闭环合格候选中按分数降序排列，选取前 10 条。该分数仅用于几何筛选和同一靶点内排序。
6. **结构输出与追溯**：为每条入选候选保存包含完整受体 A/B 链和生成肽链的复合物 PDB，同时保存单独的肽链 PDB；序列、排名、闭环距离、界面指标及来源候选记录在 CSV/JSON 中，输入口袋和生成配置也一并归档。

因此，本次设计的计算逻辑可概括为：**以已知蛋白复合物界面定义受体局部条件，以 DCFlow 从头采样环肽序列和构象，以端基闭环距离及界面接触/碰撞几何进行筛选，再按启发式指标给出每个靶点的候选优先级。**

| PDB | 靶点链 | 设计长度 | 共晶界面参考链 | 生成数 | 闭环筛选通过 | 输出 |
|---|---|---:|---|---:|---:|---|
| 6UIB | A,B | 15 aa | C | 64 | 32 | [top10 PDB 文件夹](./6UIB/top10/) |
| 8UUI | A,B | 18 aa | D | 64 | 17 | [top10 PDB 文件夹](./8UUI/top10/) |
| 9P95 | A,B | 12 aa | C | 64 | 30 | [top10 PDB 文件夹](./9P95/top10/) |

## 靶点内排名（前 10）

分数为无量纲几何启发式指标：`S = 5 Å 内靶点-肽链残基对数 - 5 × 2 Å 内严重碰撞残基对数`。分数只用于同一靶点的候选排序，不是结合自由能、结合亲和力或实验验证结果。每条记录同时给出闭环端基距离。

### 6UIB

| 排名 | 序列 | 分数 | 接触残基对 (≤5 Å) | 严重碰撞对 (<2 Å) | 环闭合 C–N (Å) | 复合物 PDB |
|---:|---|---:|---:|---:|---:|---|
| 1 | `APSAMDTGTQNAYAG` | 41 | 51 | 2 | 1.244 | [rank_01_run_more_candidate_029.pdb](./6UIB/top10/rank_01_run_more_candidate_029.pdb) |
| 2 | `TDGADNWSWADGFNS` | 41 | 46 | 1 | 1.374 | [rank_02_run_more_candidate_018.pdb](./6UIB/top10/rank_02_run_more_candidate_018.pdb) |
| 3 | `PGHATASNGTGFFPD` | 40 | 45 | 1 | 1.263 | [rank_03_run_more_candidate_011.pdb](./6UIB/top10/rank_03_run_more_candidate_011.pdb) |
| 4 | `QQVQVPDGTWAGWVV` | 37 | 42 | 1 | 1.220 | [rank_04_run_more_candidate_020.pdb](./6UIB/top10/rank_04_run_more_candidate_020.pdb) |
| 5 | `PQVDPDGNGGGSPST` | 35 | 45 | 2 | 1.403 | [rank_05_run_candidate_010.pdb](./6UIB/top10/rank_05_run_candidate_010.pdb) |
| 6 | `APYHATQTPWDSADG` | 33 | 53 | 4 | 1.264 | [rank_06_run_candidate_024.pdb](./6UIB/top10/rank_06_run_candidate_024.pdb) |
| 7 | `VYYWHGCWPGAALMA` | 32 | 52 | 4 | 1.328 | [rank_07_run_more_candidate_009.pdb](./6UIB/top10/rank_07_run_more_candidate_009.pdb) |
| 8 | `WQNTGDAGSDDIHPD` | 31 | 36 | 1 | 1.246 | [rank_08_run_candidate_015.pdb](./6UIB/top10/rank_08_run_candidate_015.pdb) |
| 9 | `QAVADTGVGGHWITP` | 30 | 55 | 5 | 1.399 | [rank_09_run_more_candidate_004.pdb](./6UIB/top10/rank_09_run_more_candidate_004.pdb) |
| 10 | `PAGSADTHSAGSHAA` | 29 | 34 | 1 | 1.294 | [rank_10_run_candidate_023.pdb](./6UIB/top10/rank_10_run_candidate_023.pdb) |

### 8UUI

| 排名 | 序列 | 分数 | 接触残基对 (≤5 Å) | 严重碰撞对 (<2 Å) | 环闭合 C–N (Å) | 复合物 PDB |
|---:|---|---:|---:|---:|---:|---|
| 1 | `VDMSFSNTNGVDNSAFNT` | 25 | 30 | 1 | 1.375 | [rank_01_run_candidate_027.pdb](./8UUI/top10/rank_01_run_candidate_027.pdb) |
| 2 | `NDYWPHGHDGAMDPSATT` | 24 | 29 | 1 | 1.250 | [rank_02_run_candidate_021.pdb](./8UUI/top10/rank_02_run_candidate_021.pdb) |
| 3 | `QNCHGAPGNSSDSAGMND` | 24 | 29 | 1 | 1.506 | [rank_03_run_candidate_026.pdb](./8UUI/top10/rank_03_run_candidate_026.pdb) |
| 4 | `DYGNAVSFSFPAFGGAAT` | 23 | 28 | 1 | 1.412 | [rank_04_run_more_candidate_000.pdb](./8UUI/top10/rank_04_run_more_candidate_000.pdb) |
| 5 | `PMYMPANHGDGGAGFSGN` | 23 | 23 | 0 | 1.474 | [rank_05_run_more_candidate_016.pdb](./8UUI/top10/rank_05_run_more_candidate_016.pdb) |
| 6 | `AGADGWMDNGDGAPYWTG` | 22 | 37 | 3 | 1.245 | [rank_06_run_more_candidate_019.pdb](./8UUI/top10/rank_06_run_more_candidate_019.pdb) |
| 7 | `SSIPWYSEHVIGWSSADE` | 22 | 22 | 0 | 1.321 | [rank_07_run_more_candidate_004.pdb](./8UUI/top10/rank_07_run_more_candidate_004.pdb) |
| 8 | `PWCTAVNGNVSTDDTDPA` | 21 | 31 | 2 | 1.211 | [rank_08_run_more_candidate_031.pdb](./8UUI/top10/rank_08_run_more_candidate_031.pdb) |
| 9 | `GTHTMGVYCAITWIHHVD` | 18 | 33 | 3 | 1.265 | [rank_09_run_more_candidate_007.pdb](./8UUI/top10/rank_09_run_more_candidate_007.pdb) |
| 10 | `ATAQPRTAGTDPAPGAPC` | 17 | 27 | 2 | 1.271 | [rank_10_run_more_candidate_005.pdb](./8UUI/top10/rank_10_run_more_candidate_005.pdb) |

### 9P95

| 排名 | 序列 | 分数 | 接触残基对 (≤5 Å) | 严重碰撞对 (<2 Å) | 环闭合 C–N (Å) | 复合物 PDB |
|---:|---|---:|---:|---:|---:|---|
| 1 | `EPGPAGDFWTFG` | 11 | 11 | 0 | 1.354 | [rank_01_run_more_candidate_024.pdb](./9P95/top10/rank_01_run_more_candidate_024.pdb) |
| 2 | `GSDEIESRFDNA` | 10 | 15 | 1 | 1.567 | [rank_02_run_candidate_005.pdb](./9P95/top10/rank_02_run_candidate_005.pdb) |
| 3 | `PWEAHGMTDFMD` | 10 | 10 | 0 | 1.203 | [rank_03_run_candidate_009.pdb](./9P95/top10/rank_03_run_candidate_009.pdb) |
| 4 | `YGHYGDDQQPHN` | 10 | 10 | 0 | 1.463 | [rank_04_run_candidate_028.pdb](./9P95/top10/rank_04_run_candidate_028.pdb) |
| 5 | `MHHWYNDSSLLK` | 9 | 9 | 0 | 1.330 | [rank_05_run_candidate_018.pdb](./9P95/top10/rank_05_run_candidate_018.pdb) |
| 6 | `SAHEWNYSDGEE` | 9 | 9 | 0 | 1.336 | [rank_06_run_more_candidate_028.pdb](./9P95/top10/rank_06_run_more_candidate_028.pdb) |
| 7 | `LNNHTFCPYAQG` | 9 | 9 | 0 | 1.264 | [rank_07_run_more_candidate_005.pdb](./9P95/top10/rank_07_run_more_candidate_005.pdb) |
| 8 | `DQNGTSGSGWPM` | 9 | 9 | 0 | 1.420 | [rank_08_run_more_candidate_011.pdb](./9P95/top10/rank_08_run_more_candidate_011.pdb) |
| 9 | `SPDYGYNGNGYD` | 8 | 13 | 1 | 1.327 | [rank_09_run_candidate_017.pdb](./9P95/top10/rank_09_run_candidate_017.pdb) |
| 10 | `GEQGDNGDEGDA` | 8 | 8 | 0 | 1.252 | [rank_10_run_more_candidate_030.pdb](./9P95/top10/rank_10_run_more_candidate_030.pdb) |

## 输入与方法

- 6UIB：靶点为 IL-23 链 A/B；原共晶肽链 C 仅用于确定结合区域中心与长度（15 aa），未将其序列或构象作为生成模板。
- 8UUI：靶点为 IL-23 链 A/B；原共晶肽链 D 仅用于确定结合区域中心与长度（18 aa），未将其序列或构象作为生成模板。
- 9P95：靶点为 integrin α4β7 链 A/B；MAdCAM-1 链 C 仅用于定位界面中心，设计长度设为 12 aa。
- 生成口袋由靶点中距界面参考配体重原子 12 Å 内且含完整 N/CA/C/O 主链的残基构成；复合物输出包含完整受体链 A/B 和生成肽链 P。
- 每个输出肽 PDB 与复合物 PDB 均写入端基 C–N `CONECT` 记录；只保留端基距离 1.2–1.6 Å 的候选。
- 两轮随机种子分别为 2026/2027/2028 和 3026/3027/3028。生成使用 DCFlow 模型 checkpoint 与 ProteinMPNN prior（`v_48_020.pt`）。

## 限制

- 当前环境没有 AlphaFold-Multimer 外部参数，因此未执行共折叠、独立结构置信度评估或 AF 结合打分。
- DCFlow 是计算生成结果；此处排序是接触/碰撞几何代理分数，不能解释为真实结合强度，也不代表生物活性、稳定性、选择性或可合成性。后续需进行结构精修、实验验证及药理/安全性评估。

## 文件

- `ranking.csv`：全部 192 条候选的闭环状态和几何指标；闭环通过者带靶点内排名。
- `top10.csv`：最终 30 条候选及 PDB 相对路径。
- `6UIB/ranking.json`、`8UUI/ranking.json`、`9P95/ranking.json`：各靶点完整有效候选排序。
- `6UIB/top10/`、`8UUI/top10/`、`9P95/top10/`：各靶点 10 个复合物 PDB 及对应肽链 PDB。
- `6UIB/input/`、`8UUI/input/`、`9P95/input/`：DCFlow 使用的受体、口袋与输入参数。
