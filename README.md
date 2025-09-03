# Nessus 报告中文转换工具

将 Nessus 扫描报告一键转换为中文 Excel（XLSX），方便导出、整理与分享。

功能特性
- 支持读取 Nessus 报告文件并转换为中文表格
- 输出格式为 Excel (XLSX)
- 自动调整表格样式（字体、对齐、边框、列宽）
- 可定制化输出目录

环境要求
- Python >= 3.8
- 支持 Windows / Linux / macOS

依赖安装
1. 确认已经安装好 Python 与 pip
2. 在命令行运行以下命令安装依赖:
   pip install -r requirements.txt

说明:
- shutil、os、datetime 是 Python 内置库，不需要额外安装
- pandas 和 openpyxl 会自动通过 requirements.txt 安装

使用方法
1. 克隆项目
   git clone https://github.com/zhkali127/nessus-report-cn.git
   cd nessus-report-cn

2. 安装依赖
   pip install -r requirements.txt

3. 运行脚本
   python main.py

4. 转换完成后，可以在 output/ 目录下找到生成的 Excel 文件

作者信息
- 作者: zhkali127
- GitHub: https://github.com/zhkali127
