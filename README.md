# Nessus CSV Processor — 漏洞扫描 CSV/XLSX 合并与报告导出工具

> 一个用于处理 Nessus 导出的 CSV/XLSX 扫描结果的轻量化工具：合并多份 CSV、匹配漏洞引用表、导出美观的漏洞报告与 IP 列表，并生成“缺失引用”示例以便补全本地漏洞库。

 [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE) [![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org)

---

## 项目简介

本脚本专注于将 Nessus（或类似工具）导出的扫描结果以 CSV/XLSX 格式进行合并与清洗，匹配本地维护的漏洞引用表（示例：`Nessus中文报告.xlsx`），生成：

* 按漏洞明细输出的 Excel 报表（带样式、列宽、边框）
* IP 列表与按风险等级统计的汇总表
* 缺失引用示例（用于标记哪些 Plugin ID 在本地引用表中缺失）
* 可选：提取并单独导出“中高危”漏洞表

该工具适合安全团队在日常漏洞处置、合规汇报与资产盘点时的中间处理脚本。

---

## 主要功能

* 自动合并当前目录下的所有 CSV 文件（若只有一个则直接读取）。
* 将 CSV 转为 XLSX 以便统一处理与美化输出。
* 加载本地漏洞引用表（`Nessus中文报告.xlsx`），根据 Plugin ID 映射中文名称、风险等级、漏洞说明与加固建议。
* 读取输入扫描表并筛选出有风险的记录（`Risk` 非 `None`）。
* 生成标准化漏洞报告（带序号、字段：IP、端口、漏洞名称、风险等级、漏洞说明、加固建议、CVE、扫描返回信息）。
* 导出 IP 列表与按风险等级统计表（`ip.xlsx`）。
* 生成缺失引用示例（`缺失示例.xlsx`），帮助维护漏洞引用库。
* 指定时间戳的输出文件名，避免覆盖历史报告。
* 格式化 Excel（表头加粗、单元格换行、列宽、边框等）。
* 从最终报告中提取并导出“中高危漏洞”（`中高危漏洞.xlsx`）。

---

## 要求

* Python 3.8+
* 推荐在虚拟环境中运行（venv / conda）。

依赖（示例）：

```
pandas
openpyxl
```

可将以下内容保存为 `requirements.txt`：

```
pandas>=1.3
openpyxl>=3.0
```

---

## 文件说明（默认/示例）

* `Nessus中文报告.xlsx`：本地维护的漏洞引用表，包含至少以下列：`编号`（Plugin ID）、`漏洞名称`（中文）、`漏洞英文名称`、`风险等级`、`漏洞说明`、`加固建议`。
* 脚本输入：当前目录下的一个或多个 `*.csv`（Nessus 导出）文件。
* 输出文件示例：

  * `漏洞扫描结果-<timestamp>.xlsx`：主报告（带样式）
  * `ip.xlsx`：IP 列表 + 漏洞风险统计
  * `缺失示例.xlsx`：缺失引用的简要与样例
  * `中高危漏洞.xlsx`：仅包含中/高/紧急风险的漏洞（若存在）

---

## 安装

```bash
git clone https://github.com/yourname/nessus-csv-processor.git
cd nessus-csv-processor
python -m venv .venv
source .venv/bin/activate  # Linux / macOS
.venv\Scripts\activate     # Windows
pip install -r requirements.txt
```

---

## 使用方法

将需要合并的 CSV 放到同一目录（脚本所在目录或以参数方式指定），确保存在 `Nessus中文报告.xlsx`（或修改脚本中的 `REFERENCE_FILE` 指向你的引用表）。

直接运行：

```bash
python nessus_processor.py
```

脚本会按以下流程执行：

1. 合并当前目录下所有 `.csv` 为 `merged.csv`（若只有一个文件则直接使用）。
2. 将 `merged.csv` 转为 `merged.xlsx` 并读取第一张表。
3. 加载 `Nessus中文报告.xlsx` 中的引用数据并构建 Plugin ID 映射。
4. 筛选出有风险（`Risk` != `None`）的漏洞记录，映射中文名称及描述，生成主报告并输出为 `漏洞扫描结果-<timestamp>.xlsx`。
5. 生成 `ip.xlsx`（IP 列表与按风险等级统计）与 `缺失示例.xlsx`（列出缺失引用的插件示例）。
6. 若存在中高危漏洞，则同时生成 `中高危漏洞.xlsx`。

---

## 输出字段说明

主报告包含以下列（并按表格样式导出）：

* `序号`：自增编号
* `IP`：资产 IP（来自输入表的 `Host`）
* `端口`：端口信息
* `漏洞名称`：优先使用引用表中的中文名称，若无则使用原始 `Name` 字段
* `风险等级`：引用表中 `风险等级`，或根据 `Risk` 字段映射（示例：`Critical->紧急`）
* `漏洞说明`：引用表中的说明或输入表中的 `Synopsis/Description`
* `加固建议`：引用表中的建议或输入表中的 `Solution`
* `CVE`：CVE 标识（若有）
* `扫描返回信息`：原始插件输出（Plugin Output）

---

## 常见问题与排错

* **脚本提示找不到 `Nessus中文报告.xlsx`**：请确保文件存在于脚本目录，或修改脚本中 `REFERENCE_FILE` 常量指向正确路径。
* **CSV 编码导致读取异常**：请确认 CSV 文件为 UTF-8（带 BOM 的 `utf-8-sig` 更兼容），脚本对常见编码会有一定容错。
* **输出 Excel 无法打开或显示损坏**：检查是否有同名文件被 Excel 占用，或系统权限导致无法替换文件。
* **缺失引用样例过多**：说明本地漏洞引用表未覆盖扫描插件库，建议补充 `Nessus中文报告.xlsx` 中的记录或同步最新插件信息。

---

## 作者

作者：`zhkali`
