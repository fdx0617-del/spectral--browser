[README.md](https://github.com/user-attachments/files/33367901/README.md)
# 🌍 地物波谱库浏览器 (Spectral Library Browser)

## 📖 项目简介

本项目是一个基于 Web 的交互式地物波谱库浏览器。通过整合 **USGS 光谱库**与卫星传感器（**Landsat-8 OLI** / **Sentinel-2 MSI**）的真实光谱响应函数（SRF），实现了地物光谱的交互式对比与卫星波段反射率的科学模拟。

该系统打破了传统波谱库“静态表格”的局限，通过可视化交互和知识服务，让用户直观理解不同地物在特定卫星波段下的波谱特征，为遥感定量反演提供可靠的先验知识。

## ✨ 核心功能

1. **多维光谱对比**：
   - 按类别（植被、水体、土壤、矿物、人造材料）筛选样本。
   - 支持关键字搜索与多选，Plotly 图表实时绘制多条地物光谱曲线。
   - 悬浮图表可查看任意波长的单点瞬时反射率。
2. **传感器波段模拟与叠加**：
   - 支持切换 Landsat-8 与 Sentinel-2 传感器。
   - 图表上以半透明区域叠加真实卫星波段范围，并支持点击交互。
3. **真实 SRF 光谱响应加权计算**：
   - 系统内置真实 SRF 数据，通过**线性插值 + 梯形积分**算法计算波段内的加权平均反射率，优于简单平均。
   - 包含 SRF 覆盖率校验（<95% 自动判定为数据不足），确保物理真实性。
4. **波谱知识科普**：
   - 点击波段后，系统动态加载 `band_explanation.json`，展示该波段的物理意义与典型应用场景（如 B6 短波红外对植被水分敏感）。

## 🔬 科学原理

卫星传感器接收到的能量并非单一波长，而是波段范围内所有能量的积分。因此本项目采用 SRF 加权积分公式：

$$R_{band} = \frac{\int R(\lambda) \cdot S(\lambda) d\lambda}{\int S(\lambda) d\lambda}$$

其中：
- $R(\lambda)$：地物在波长 $\lambda$ 处的反射率
- $S(\lambda)$：传感器在波长 $\lambda$ 处的光谱响应值

由于光谱和 SRF 数据都是离散且非均匀采样的，系统使用了**梯形积分法**实现该公式，避免了简单平均带来的偏差。

## 📊 数据来源与查证

本项目所有数据均来自官方公开数据，并标注查证日期：

| 数据内容 | 来源 | 查证日期 |
| :--- | :--- | :--- |
| 地物光谱数据 | [USGS Spectral Library Version 7](https://www.usgs.gov/) | 2026-10-13 |
| Landsat-8 OLI 波段参数与 SRF | NASA / USGS 官方发布 | 2026-10-13 |
| Sentinel-2 MSI 波段参数与 SRF | ESA 官方发布 | 2026-10-13 |
| 可视化库 | [Plotly.js](https://plotly.com/javascript/) (MIT License) | 2026-10-13 |

> **免责声明**：本作品使用 AI 工具辅助开发，但开发组对作品的每一个数字和结论负责。

## 🛠️ 技术栈

- **前端**：HTML5, CSS3, 原生 JavaScript (ES6)
- **可视化**：Plotly.js
- **数据格式**：JSON
- **架构**：纯前端静态页面（无需后端服务器）

## 🚀 本地运行指南

> ⚠️ **注意**：由于浏览器同源策略（CORS），直接双击 `index.html` 无法加载本地 JSON 数据。
> 必须通过本地服务器运行。

**方法一：使用 VS Code (推荐)**
1. 用 VS Code 打开项目文件夹。
2. 安装 `Live Server` 插件。
3. 右键 `index.html`，选择 `Open with Live Server`。

**方法二：使用 Python**
1. 在项目根目录打开终端（命令行）。
2. 输入 `python -m http.server 8000`。
3. 浏览器访问 `http://localhost:8000`。

## 📂 项目结构

```text
├── index.html                 # 主页面
├── style.css                  # 样式表
├── app.js                     # 核心逻辑（绘图、SRF 积分、交互）
├── data/                      # 光谱数据与科学解释
│   ├── vegetation_batch.json
│   ├── water.json
│   ├── soil.json
│   ├── mineral.json
│   ├── manmade.json
│   └── band_explanation.json  # 波段科普文案
└── sensor/                    # 传感器参数与 SRF
    ├── landsat8.json
    ├── sentinel2.json
    └── srf/
        ├── landsat8_srf.json
        └── sentinel2_srf.json
