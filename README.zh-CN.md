# ER/RN 轨道比较

[English](README.md)

论文 *Nearly indistinguishable: charged black holes in general and entangled
relativity* 的配套代码。

如需使用论文当时发布的版本，请下载 [Yuan2026_10_14.zip](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/Yuan2026_10_14.zip)，并参照[当时的运行说明](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/README.zh-CN.md)。

## 当前版本

在 [GitHub 发布页](https://github.com/JianqingYuan/Yuan2026_10_14/releases) 的 **Assets** 下下载所需的压缩包并解压。

- `Yuan2026_11_07_general_spherical_v9_4_B4.zip` 是 B4 轨道拟合程序。使用说明见包内的 `README.zh-CN.md`，B4 拟合指南见 `README_NEXTGEN.zh-CN.md`。
- `periapsis-profile-0.1.1.zip` 用已有拟合计算每个径向周期的近心点进动误差。可读入 B4 的拟合结果，也可使用自己的拟合，支持独立安装。包内 `README.md` 以第 36 组 RN 拟合为例说明用法。

如要用 B4 重画论文的图，请使用 B4 目录里的论文入口。安装 Python 3.12 后，
进入同时包含 `main.py` 和 `requirements.txt` 的文件夹，
在地址栏输入 `cmd`，打开命令窗口，依次运行：

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
python main.py
```

默认从保存的数据重画全部 13 张论文图，不重新拟合。
PDF 和 PNG 位于 `Yuan2026_11_07_general_spherical_v9_4_B4/output/paper_<时间戳>/figures/`。
保存数据已同步四对 S301 和 77 个极端 ER 拟合的已复核修正，
对应修订稿图 13 和图 12；其余原有数值保留。

如需现场试算，改用 `python main.py --mode demo`。
它安排 42 次模型拟合、使用有限预算；部分结果可能待复核或缺失，
不会用保存的论文拟合填补缺口，也不代表重新验证了论文的统计结论。
完整重算是另一种计算量大得多的运行方式。

## 详细说明与证据

在 `Yuan2026_11_07_general_spherical_v9_4_B4` 内查看：

- `README.zh-CN.md`：安装、运行模式及可选高级设置。
- `AUDIT_STATUS.md`：审计范围与限制。
- `VALIDATION.json`：验证记录和数据身份。

独立附件：

- [原始选定端点证据](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/audit_evidence_20261015.zip)

证据 ZIP 是独立审计记录，不是重复的程序压缩包。
针对性修正和软件测试不证明全局最优，也不代表重验了全部历史拟合。
默认重画无需额外修改设置。
