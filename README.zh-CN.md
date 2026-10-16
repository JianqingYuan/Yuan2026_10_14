# ER/RN 轨道比较

[English](README.md)

论文 *Nearly indistinguishable: charged black holes in general and entangled
relativity* 的配套代码。

## 从这里开始

程序位于 `Yuan2026_10_14` 子目录，GitHub 上提供同名的
`Yuan2026_10_14.zip`。如下载的是 ZIP，先解压。安装 Python 3.12 后，
进入同时包含 `main.py` 和 `requirements.txt` 的文件夹，
在地址栏输入 `cmd`，打开命令窗口，依次运行：

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
python main.py
```

默认从保存的数据重画全部 13 张论文图，不重新拟合。
PDF 和 PNG 位于 `Yuan2026_10_14/output/paper_<时间戳>/figures/`。
保存数据已同步四对 S301 和 77 个极端 ER 拟合的已复核修正，
对应修订稿图 13 和图 12；其余原有数值保留。

如需现场试算，改用 `python main.py --mode demo`。
它安排 42 次模型拟合、使用有限预算；部分结果可能待复核或缺失，
不会用保存的论文拟合填补缺口，也不代表重新验证了论文的统计结论。
完整重算是另一种计算量大得多的运行方式。

## 详细说明与证据

解压后，在 `Yuan2026_10_14` 内查看：

- `README.zh-CN.md`：安装、运行模式及可选高级设置。
- `AUDIT_STATUS.md`：审计范围与限制。
- `VALIDATION.json`：验证记录和数据身份。

独立附件：

- [原始选定端点证据](audit_evidence_20261015.zip)

证据 ZIP 是独立审计记录，不是重复的程序压缩包。
针对性修正和软件测试不证明全局最优，也不代表重验了全部历史拟合。
默认重画无需额外修改设置。
