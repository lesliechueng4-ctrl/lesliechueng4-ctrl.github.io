---
title: "桌面文件自动整理工具：告别混乱的桌面"
date: 2026-02-14T17:36:00+08:00
draft: false
tags: ["Python", "工具", "自动化", "Windows"]
categories: ["问题解决"]
---

桌面文件越来越多？找文件像大海捞针？做了个桌面文件自动整理工具，解放双手。

## 背景

我是个典型的"桌面堆放型"用户。截图、下载的文件、临时的文档... 全都堆在桌面上，时间一久就变成"文件坟场"。

每次找文件都要翻半天，手动整理又太耗时。于是写了个 Python 脚本来自动化这个任务。

## 功能特性

✨ **智能分类** - 按文件类型自动归档
📅 **日期归档** - 按修改日期创建子文件夹
🔄 **去重清理** - 检测并删除重复文件
📝 **详细日志** - 记录所有操作
↩️ **一键回滚** - 不满意随时恢复
👀 **模拟运行** - 预览操作，避免意外

## 文件分类规则

| 分类 | 支持的扩展名 |
|------|-------------|
| Images | .jpg, .png, .gif, .bmp, .webp, .svg 等 |
| Documents | .pdf, .doc, .docx, .txt, .xlsx, .md 等 |
| Videos | .mp4, .avi, .mkv, .mov, .flv 等 |
| Audio | .mp3, .wav, .flac, .aac 等 |
| Archives | .zip, .rar, .7z, .tar 等 |
| Installers | .exe, .msi, .appx 等 |
| Code | .py, .js, .html, .css, .java等 |

## 使用方法

### 基本使用

```bash
# 整理当前用户桌面
python desktop_cleaner.py
```

### 模拟运行（推荐首次使用）

```bash
# 先看看会发生什么
python desktop_cleaner.py --dry-run
```

### 整理指定文件夹

```bash
# 整理下载文件夹
python desktop_cleaner.py -d "C:\Users\YourName\Downloads"
```

### 回滚操作

```bash
"后悔了？一键恢复"
python desktop_cleaner.py --rollback
```

## 整理后的效果

整理前：
```
桌面/
├── 截图20240115.jpg
├── 截图20240116.png
├── report.pdf
├── notes.txt
├── tutorial.mp4
└── backup.zip
```

整理后：
```
桌面/
├── Images/
│   └── 2024-01-15/
│       ├── 截图20240115.jpg
│       └── 截图20240116.png
├── Documents/
│   └── 2024-01-15/
│       ├── report.pdf
│       └── notes.txt
├── Videos/
│   └── 2024-01-16/
│       └── tutorial.mp4
├── Archives/
│   └── 2024-01-15/
│       └── backup.zip
└── desktop_organizer.log
```

## 技术实现

### 重复文件检测

使用 MD5 哈希算法计算文件内容的指纹：

```python
import hashlib

def calculate_hash(filepath):
    hasher = hashlib.md5()
    with open(filepath, 'rb') as f:
        while chunk := f.read(8192):
            hasher.update(chunk)
    return hasher.hexdigest()
```

即使文件名不同，内容相同也能检测出来。

### 回滚机制

记录所有移动操作到 JSON 文件：

```json
{
  "timestamp": "2024-01-15T10:30:45",
  "operations": [
    {
      "original_path": "photo.jpg",
      "new_path": "Images/2024-01-15/photo.jpg"
    }
  ]
}
```

回滚时按相反顺序执行操作，恢复文件到原始位置。

### 安全保护

自动排除脚本自身、日志文件和回滚文件，避免误删。

## 遇到的问题

### 1. 文件名冲突

如果目标位置已有同名文件，自动重命名：
```
photo.jpg -> photo_1.jpg -> photo_2.jpg
```

### 2. 文件正在使用

如果文件被其他程序占用，跳过并记录警告。

### 3. 网络路径

网络路径访问较慢，建议整理本地文件夹。

## 使用建议

1. **首次使用**: 先用 `--dry-run` 模拟运行，确认无误后再执行
2. **定期整理**: 可以设置定时任务，每天或每周自动整理
3. **自定义规则**: 可以修改脚本中的 `FILE_CATEGORIES` 添加自己的分类
4. **重要备份**: 虽然有回滚功能，重要文件还是建议备份

## 性能

- 处理速度：约 100-500 文件/秒
- 内存占用：约 10-50MB
- 大文件去重：需要更多时间计算哈希

## 下一步计划

- [ ] 添加图形界面（GUI）
- [ ] 支持自定义分类规则配置文件
- [ ] 添加文件大小统计
- [ ] 支持按内容关键词分类
- [ ] 集成到系统右键菜单

## 总结

这个小工具解决了我多年的痛点。现在桌面永远整洁，找文件也快了很多。自动化真的很香！

---

**项目版本**: 1.0.0
**代码仓库**: [GitHub](https://github.com/lesliechueng4-ctrl)

*注：适用于 Windows 系统，需要 Python 3.6+*
