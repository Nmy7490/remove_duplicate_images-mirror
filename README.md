
# 图片/视频重复文件检测器

一个用于扫描文件夹中的图片与视频文件，自动检测重复文件（基于 pHash 感知哈希算法），并可选择安全记录或直接删除重复文件。  
支持批量处理、视频抽帧、哈希缓存、GUI 可视化操作与多进程加速。

---

## 目录结构示例
```

duplicate\_checker/
├─ duplicate\_checker.py      # 主脚本（含 GUI 与 remove\_duplicates()）
├─ README.md
├─ requirements.txt
├─ file\_hashes.json          # 哈希缓存（自动生成）
└─ duplicates.json           # 重复文件记录（自动生成）

````

---

## 功能概览
- 支持图片格式：`.jpg`, `.jpeg`, `.png`, `.bmp`, `.gif`  
- 支持视频格式：`.mp4`, `.avi`, `.mov`, `.mkv`
- 对视频进行抽帧处理（默认每 30 帧抽取一帧）
- 基于感知哈希（pHash）判断文件相似度，检测重复文件
- 可选择安全模式（仅记录重复文件）或删除模式（直接删除重复文件）
- 哈希缓存（`file_hashes.json`）避免重复计算
- 重复文件记录（`duplicates.json`）
- GUI 可视化操作，无需命令行即可使用
- 多进程加速，提高大批量文件处理效率

---

## 环境要求
- Python 3.8 或更高（推荐 3.9+）
- 系统需安装以下 Python 包（见 `requirements.txt`）
- Windows/macOS/Linux 通用

---

## 快速安装（本地环境）

> 推荐在虚拟环境中安装依赖，避免污染系统 Python

```bash
# 1. 克隆仓库（如果尚未克隆）
git clone <your-repo-url>
cd duplicate_checker

# 2. 创建并激活虚拟环境
python -m venv venv
# Windows
venv\Scripts\activate
# macOS / Linux
source venv/bin/activate

# 3. 安装依赖
pip install -r requirements.txt
````

---

## 如何运行

### 1) GUI（推荐）

直接运行主脚本：

```bash
python duplicate_checker.py
```

执行后会弹出窗口，通过 GUI 设置：

* 扫描根目录：要检测的文件夹（支持递归子文件夹）
* 视频抽帧间隔（FRAME\_INTERVAL）：每隔多少帧抽一帧
* pHash 相似度阈值（PHASH\_THRESHOLD）：相似度判断标准
* 安全模式：勾选后仅记录重复文件，不删除

点击“开始扫描”后程序会自动处理所有图片和视频，并显示进度和日志。

---

### 2) 命令行/无 GUI（可选）


## 参数说明（源码顶部常量，可通过 GUI 修改或源码调整默认值）

* `IMAGE_EXTENSIONS`：支持的图片后缀列表
* `VIDEO_EXTENSIONS`：支持的视频后缀列表
* `FRAME_INTERVAL`（默认 30）：视频抽帧间隔
* `PHASH_THRESHOLD`（默认 5）：pHash 相似度阈值，值越小要求越严格
* `HASH_FILE`：哈希缓存文件名，避免重复计算
* `DUPLICATE_FILE`：重复文件记录文件名
* `SAFE_MODE`（默认 False）：True=安全模式（仅记录重复），False=删除重复文件

---

## 输出说明

* 重复文件会记录在 `duplicates.json`
* 哈希缓存保存到 `file_hashes.json`
* 删除模式下，重复文件会直接被删除

---

## 日志信息说明（GUI 或控制台输出）

* `[info]` — 信息提示，例如扫描开始/结束
* `[progress]` — 当前处理进度
* `[added]` — 新增哈希成功
* `[duplicate]` — 检测到重复文件
* `[deleted]` — 删除重复文件（删除模式下）
* `[error]` — 异常或无法处理的文件

---

## 常见问题与排查

**1. 视频无法读取或抽帧失败**

* 确认视频文件可用，且 OpenCV 支持格式

**2. 删除失败 / 权限不足**

* 检查文件是否被其他程序占用或磁盘权限不足
* 安全模式下不会删除文件

**3. 处理大量文件速度慢**

* 程序使用多进程加速，可根据 CPU 核心数调整
* 视频处理较慢，适当增加 `FRAME_INTERVAL` 可加速

* `FRAME_INTERVAL`（默认 30）：视频抽帧间隔，即每隔多少帧抽取一帧用于生成 pHash。
  - **作用**：值越小抽帧越多，重复检测越精确，但处理时间和内存占用增加；值越大抽帧越少，速度快，但可能漏检局部重复视频。
  - **参考范围**（假设视频帧率约 25~30 fps）：

    | 视频长度/大小 | 建议 FRAME_INTERVAL |
    |---------------|------------------|
    | 短视频 (<1 分钟，文件小于 500MB) | 10~30 |
    | 中等视频 (1~10 分钟，文件 500MB~2GB) | 20~50 |
    | 长视频 (>10 分钟或大文件 >2GB) | 50~100 |

  - **经验法则**：抽取总帧数约 200~500 帧即可覆盖大部分视频内容，兼顾检测准确性与处理速度。
