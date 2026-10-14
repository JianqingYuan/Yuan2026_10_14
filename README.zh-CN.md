# ER/RN 轨道比较程序

[English](README.md)

## 1. 物理情景

本程序配合论文 *Nearly indistinguishable: charged black holes in general and
entangled relativity*，比较两种静态球对称带电时空中的中性试验粒子与光线运动：
纠缠相对论（ER）和广义相对论的 Reissner–Nordström（RN）解。

时序实验使用 ER 生成的理想平面轨道，分别考虑无噪声和高斯噪声观测。
ER 与 RN 对同一组位置数据拟合，各自调整六个中心天体及轨道参数。
轨道由 DOP853 积分，拟合使用差分进化（DE）和信赖域反射最小二乘（TRF）。

## 2. 程序结构

- `main.py`：启动计算和绘图的主程序。
- `settings.py`：读者通常只需修改这里的设置。
- `engine/`：新旧两套拟合流程的统一接口。
- `vendor/`：保留的轨道积分与 DE/TRF 流程代码。
- `experiments/` 和 `configs/`：数据集选择和实验配置。
- `data/observations/`：重新拟合时使用的位置观测数据。
- `data/paper/`：用于直接重画论文图的已有数值结果。
- `plotting/`：绘图程序；`tests/`：回归测试。
- `output/`：运行后生成的新结果和图片。

更多细节见[实验说明](experiments/README.txt)、
[已有结果的数据格式](data/paper/SCHEMA.txt)和[验证记录](VALIDATION.json)。

## 3. 运行方式

### Windows 第一次使用

**第 1 步：解压整个代码包。**
打开解压后的文件夹，进入能同时看到 `main.py` 和 `requirements.txt` 的位置。

**第 2 步：安装 Python 3.12。** 
如果已经安装，可以跳过。
安装方法见 [Python 官方 Windows 安装说明](https://docs.python.org/3.12/using/windows.html)，
安装时保留 Python 启动器（`py`）和 pip。

**第 3 步：准备本代码包专用的 Python 环境，仅首次需要。**
在刚才的代码文件夹窗口中，点击顶部地址栏，输入 `cmd` 并按回车。
这会打开已经位于该文件夹的 CMD 窗口。在里面依次输入：

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
```
等待安装

**第 4 步：生成图片。** 
在同一个 CMD 窗口中输入：

```console
python main.py
```

当 `settings.py` 中为 `MODE = "paper"` 时，这一步会从已有数值结果重画论文全部 13 张图，不重新拟合。
如果已经改成 `MODE = "demo"`，同一条命令会启动少量数据的实际试算。


**第 5 步：查看图片。** 
回到文件目录，程序会创建一个新文件夹： `output`、刚生成的
`paper_<时间戳>` 文件夹、`figures`。双击其中的 PDF 或 PNG 即可查看。
一共 **13 个 PDF 和 13 个 PNG**

### 之后使用
同一个代码包文件夹地址里重新打开 CMD，再依次输入：

```console
.venv\Scripts\activate
python main.py
```

每次新开 CMD 窗口，都需要先激活 `.venv`。


## 4. 运行配置
### 怎样修改运行内容？

在CMD 窗口中输入：

```console
notepad settings.py
```

在打开的记事本中，按下文说明修改已有的设置行。
回到 CMD 窗口，再输入 `python main.py`，程序就会读取新设置。选择模式不需要修改 `main.py`。

- `MODE` 决定**做什么**：重画论文图、小规模试算，完整重算。
- `SUITE` 决定**拟合哪一组实验数据**。
- `FIT_PIPELINE` 决定**用哪一版拟合流程**处理这些数据。
- `TASK_RANGE` 决定重算时**执行哪些编号的小任务**。

### 选择模式：你希望得到什么？

**A. 直接查看论文图：`MODE = "paper"`（默认）**

```python
MODE = "paper"
```

读取 `data/paper/`，生成 **13 个 PDF 和 13 个 PNG**，保存在`output/paper_<时间戳>/figures/`，不进行 DE/TRF 拟合。
此时，`SUITE`、`FIT_PIPELINE`、`TASK_RANGE` 和核数设置都不改变绘图结果。
如果只想重画个别几张图，比如图4，6，7，在已激活 `.venv` 的 CMD 窗口中，
用下面这条命令代替单独的 `python main.py`：

```console
python main.py --mode paper --figures 4 6 7
```

**B. 少量数据实际试算：`MODE = "demo"`**

第一次试算，可在 `settings.py` 中把对应几行改成下面这样

```python
MODE = "demo"
SUITE = "baseline"
FIT_PIPELINE = "paper"
WORKERS = 1
CORES_PER_FIT = 1
```
保存后回到 CMD 窗口输入再次运行main文件


这会从基本实验中选取一组无噪声数据和一组有噪声数据，分别由 ER 和 RN 拟合，共4 个模型拟合。观测点和拟合范围不变，但 DE/TRF 的计算预算明显缩小。

程序执行到最终绘图时，会生成各个实验组的最佳拟合概览 `new_run_summary.pdf/.png`，
以及两张展示 x(t)、y(t)时序观测结果的拟合曲线 `demo_timeseries_*.pdf/.png`。
时序图需要对应的数据集已有可用拟合结果。
这不是论文全部 13 张图的缩小版；拟合可能尚未收敛，不能作为论文统计结论。
如果改为 `SUITE = "all"`，则三个实验各试算两组数据，共 12 个模型拟合。
由于保留完整观测时段，尤其是高偏心率 S301 轨道，`all` 试算仍可能耗时几十分钟。
第一次建议先选 `baseline`。

`DEMO_FINISHED_WITH_ITERATION_LIMITS` 表示演示用的小预算已用完，不代表拟合收敛。
真正的报错或 `FAILED` 与此不同，不能当作演示成功。

若只想再少算一点——同一组数据各做一次 ER 和 RN 拟合——
则在 CMD 窗口中改用下面这条命令：

```console
python main.py --mode demo --suite baseline --max-tasks 2
```

**C. 用完整预算计算新结果：`MODE = "recompute"`**

若要重算全部实验，在 `settings.py` 中修改以下几行。
这个例子最多使用 8 个 CPU 核；若电脑可用核数不足 8，请把 `WORKERS` 改小：

```python
MODE = "recompute"
SUITE = "all"
FIT_PIPELINE = "paper"
WORKERS = 8
CORES_PER_FIT = 1
TASK_RANGE = None
```

在 `TASK_RANGE = None` 时，这会进行 **15,660 个模型拟合**，并从方程重新计算图 1–5。
图 6–13 只使用本次新结果，在各图所需的 ER/RN 成对结果齐全且满足绘图条件后生成。
所需结果全部具备时，可得到**论文形式的 13 张图，
外加一张结果概览图**，每张均输出 PDF 和 PNG。
如果某张图所需的数据尚不齐全，最终绘图阶段仍会生成概览，但不会生成该张论文统计图。

这是大规模计算，不是短时间的本机演示；增加核数也不保证特定完成时间或相同最优点。
正式开始前，先在 CMD 窗口中输入下面这条命令，可以查看目前如果执行recompute模式时的任务计划，不执行拟合：

```console
python main.py --mode recompute --dry-run
```

确认计划符合预期后，再正常`python main.py`


### 选择实验数据：`SUITE`

只影响 `demo` 和 `recompute`，不影响直接` paper `重画论文图。
以下数量和图号指的是完整重算：

- `"baseline"`：第六章之前的基本实验。共 **12,960 个模型拟合**，对应图 **6–11**。
- `"extreme"`：第六章之前的强场高电荷实验。共 **2,592 个模型拟合**，对应图 **12**。
- `"s301"`：第六章的 S301 类轨道实验。共 **108 个模型拟合**，包括 6 对无噪声拟合、48 对 **30 微角秒**噪声拟合；其中有噪声结果对应图 **13**。
- `"all"`（默认）：以上三个实验全部运行，共 **15,660 个模型拟合**；
  完整运行还会计算图 **1–5**。

同一组观测数据由 ER 和 RN 分别拟合，所以一对结果算两个模型拟合。
改变 `SUITE` 会改变使用哪些观测数据、计算量和能够生成哪些统计图，
但它本身并不是“强制切换拟合代码版本”的开关。

### 选择拟合版本：`FIT_PIPELINE`
只影响 `demo` 和 `recompute`，不影响直接` paper `重画论文图。
它选择的是数值计算流程，不是 ER 或 RN 理论；两种理论仍都会拟合。

- `"paper"`（默认）：按论文自动选择。`baseline` 和 `extreme` 使用 **legacy 旧版**，`s301` 使用 **s301 新版**。
- `"legacy"`：所选实验全部使用旧版流程。ER/RN 成对处理，并在两模型之间传递候选解。
- `"s301"`：所选实验全部使用新版 S301 双来源流程。ER 和 RN 的拟合任务独立安排。

例如，`SUITE = "baseline"` 配合 `FIT_PIPELINE = "s301"`，意思是“拿基本实验的观测数据，用新版流程重新拟合”

两版尚无明确的普遍上下位关系：第一版用于此前的实验，第二版针对 S301 类高偏心率轨道发展。数据集的积分设置保持不变；种子和数值检查策略见实验说明。
固定种子也不保证不同运行环境下逐位一致。

### 分配计算核心：`WORKERS` 与 `CORES_PER_FIT`

- `WORKERS`：允许使用的**总 CPU 核数**，不是实验组数。默认 `1`，可设为不超过本机可用核数的正整数。
- `CORES_PER_FIT`：**一次拟合内部**分配多少核。一般保持默认 `1`，把总核数用于并行处理不同拟合。

例如，`WORKERS = 8`、`CORES_PER_FIT = 1` 表示最多允许 8 个工作单元同时运行。
这里的“工作单元”就是程序一次安排的一项任务，两版打包任务的方式不同：

- **legacy 旧版：同一组数据的 ER 和 RN 合起来算一项任务。**
  例如，对“数据 A”用 ER 拟合、用 RN 拟合，都属于同一个成对流程。
  这个流程按既定顺序执行各个阶段，必要时在两种模型之间传递候选解，
  整体占用一个单核计算名额，而不是让 ER 和 RN 各占一个核同时计算。
  因此，8 个名额最多让 **8 组数据的成对流程同时推进**，不是 16 个模型同时计算。
  某一组的整个成对流程结束后，程序才会把这个空位交给下一组等待的数据。

- **S301 新版：一组数据的一种模型拟合，单独算一项任务。**
  例如，“数据 A 的 ER 拟合”和“数据 A 的 RN 拟合”是两项独立任务，各占一个名额。
  因此，8 个名额最多让 **8 个模型拟合同时推进**；它们可以来自不同数据组，
  不要求始终凑成 4 对 ER/RN。某个模型拟合结束后，其空位就可以接续下一项任务，
  不必等同一组数据的另一个模型也算完。


如果所选任务全部使用新版 S301 流程，改为 `WORKERS = 8`、`CORES_PER_FIT = 2`，
则最多同时进行 4 个拟合，每个拟合内部使用 2 核；这样不一定更快。
**只要用到legacy旧版，就必须保持 `CORES_PER_FIT = 1`**，默认自动选版本的 `all` 也一样。
核数设置改变的是计算资源分配，不改变观测数据、噪声档位或拟合总数量。

### 按编号区间拆分重算：`TASK_RANGE`

`TASK_RANGE = None`（默认）表示执行全部任务。如果只执行第 1 至第 50 项，
在 `settings.py` 中把已有的设置行改为：

```python
TASK_RANGE = (1, 50)
```

编号从 **1** 开始，区间**包含首尾两项**。这个设置与命令选项
`--task-range 1 50` 等价，**仅在 `recompute` 生效**；`paper` 和 `demo`
会忽略它。区间不能与 `--task-index` 或 `--max-tasks` 同时使用。

这里编号的“小任务”就是上文的工作单元：**legacy 的一整对 ER/RN 算一项，
S301 的单个模型拟合算一项**。使用相同代码、`SUITE`、`FIT_PIPELINE`、
模型选择及排列顺序时，任务顺序固定。默认模型为 ER 和 RN；高级选项
`--models` 可以改变模型选择。改变所选模型或模型顺序都可能改变任务编号。
各节点必须保持这些选择以及其他实验设置一致。

默认同时选择 ER 和 RN 时，完整任务数量如下：

下面 `paper`、`legacy`、`s301` 三列分别是选择对应 `FIT_PIPELINE` ，列表示此时三个实验部分计划计算的小任务数；
最后一列 `model fits` 是模型拟合数，因为要计算两个理论所以是*2：

```text
SUITE       paper   legacy     s301   model fits
baseline    6,480    6,480    12,960       12,960
extreme     1,296    1,296     2,592        2,592
s301          108       54       108          108
all         7,884    7,830    15,660       15,660
```

在 `SUITE="all"`、`FIT_PIPELINE="paper"` 时，**1–6,480** 属于
`baseline`，**6,481–7,776** 属于 `extreme`，**7,777–7,884** 属于 `s301`。
改选其他实验或流程后，其任务列表会重新从 1 编号。
这里统计的是不同流程的工作单元数；数值流程本身的区别见前面的拟合版本说明。

分配区间前，可以先查看计划，不进行轨道积分或拟合：

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 1 50 --dry-run
```

预览会显示总工作单元数、所选区间及选中的模型拟合数。在这个例子中，完整计划是
**7,884 个小任务**，其中 **1–50** 是
**50 个成对小任务／100 个模型拟合**。

### 在已分配的计算节点上分别运行，再汇总结果

先按计算中心的正常流程取得节点，准备好 Python 环境，然后**在已分配的节点上**，
进入代码包目录执行以下命令。这些设置只限制程序内部的用核数量，
不会申请节点、提交作业或跨节点调度。

如果一个节点分配到 50 核，使用 `--workers 50 --cores-per-fit 1`。
第一个节点执行 1–50，第二个节点执行 51–100，依次分配互不重叠的区间，
直到覆盖全部任务。各节点使用相同代码和配置，只改变区间、可用核数及输出目录。
如果共用同一个代码目录，建议按下例使用命令选项；
**不要在任务运行期间修改共享的 `settings.py`**。

在第一个节点执行：

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 1 50 --workers 50 --cores-per-fit 1 --output output/node01
```

在第二个节点执行：

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 51 100 --workers 50 --cores-per-fit 1 --output output/node02
```

每个区间使用独立的输出目录。最后一段可以少于 50 项，例如默认 `all` 计划的
7,851–7,884。50 项任务最多可以同时占用 50 个单核名额，但各项耗时不同，
或剩余任务不足时，部分核会空闲；不能保证 50 核始终用满。

各节点运行结束后，把输出目录放到同一台机器可访问的位置，可以使用共享存储，
也可以先复制到汇总机器，再执行：

```console
python main.py --collect output/node01 output/node02 --output output/combined
```

实际汇总时应列出所有需要合并的节点输出目录，不只限于示例中的两个。
各份结果须来自相同代码、实验选择、拟合流程、模型选择与顺序及实验设置。
若重复结果之间存在冲突，汇总会拒绝，不会擅自挑选卡方最小的一份。
缺失结果不会用论文已有拟合补齐。

各个区间运行时不计算几何图 **1–5**。汇总得到 `SUITE="all"` 的完整结果，
且结果满足绘图条件后，才统一计算一次这些几何图。
图 **6–13** 仍要求各图所需的结果网格齐全且满足绘图条件。
因此，只汇总第 1–100 项不会得到论文全部图片。

### 查看输出：`OUTPUT_ROOT`

默认 `OUTPUT_ROOT = "output"`，每次在程序目录的 `output/` 下新建一个带时间戳的
文件夹，例如 `output/demo_<时间戳>/`。改成其他目录只改变保存位置，不会覆盖此前的运行结果或随程序提供的数据。

- 所有模式：`figures/` 保存生成的图片，`run.json` 记录本次运行信息。
- 拟合模式另有：`tasks/` 保存各任务输入与结果，`progress.json` 记录任务进度；
  执行到最终绘图后，`plot_status.json` 记录图 6–13 中哪些已经生成。
  若中途停止，可能已有部分结果，但尚未进行最终绘图。

也可以在命令后面添加选项，临时覆盖 `settings.py` 中的对应设置，只对本次运行生效。
没有 `--task-range` 时，重算仍会读取已保存的 `TASK_RANGE`。
要用下面的例子执行全部任务，请先在 `settings.py` 中恢复 `TASK_RANGE = None`
并保存，再在 CMD 窗口中输入这条命令，**正式启动**全部重算，最多使用 8 核：

```console
python main.py --mode recompute --suite all --fit-pipeline paper --workers 8 --cores-per-fit 1
```

更多选项见 `python main.py --help`。其中 `--resume PATH` 要求使用相同任务计划，
并复用已结束任务；若发现已保存但未完成的任务，会拒绝继续，
不会跳过它们或自动重启中断的优化器。

### 遇到问题时

- **提示找不到 `main.py` 或 `requirements.txt`：** 先进入能看到这两个文件的文件夹，
  再从地址栏输入 `cmd` 打开命令窗口。
- **提示缺少某个库：** 在当前 CMD 中激活 `.venv`，
  再执行一次 `python -m pip install -r requirements.txt`。
- **只是想看图：** 保持 `MODE = "paper"`；不要误选需要大量计算的 `recompute`。
- **想中途停止拟合：** 在 CMD 中按一次 Ctrl+C，等待程序停止。
  已写入的文件会保留，但被中断的优化器并不能自动接着算。

macOS/Linux 用户请在解压后的代码包目录中打开终端。安装 Python 3.12 后，
用 `python3.12 -m venv .venv` 创建环境、用 `source .venv/bin/activate` 激活环境，
之后使用上文相同的 `python` 命令。修改 `settings.py` 时使用任意纯文本编辑器，
不使用 Windows 记事本命令。详见 [Python 环境说明](https://docs.python.org/3.12/library/venv.html)。
