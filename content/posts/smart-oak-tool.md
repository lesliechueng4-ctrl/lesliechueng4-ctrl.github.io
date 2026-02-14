---
title: "Smart OAK 工单自动化工具：提升 IT 运维效率"
date: 2026-02-14T19:20:00+08:00
draft: false
tags: ["Python", "自动化", "工具", "IT运维"]
categories: ["项目实践"]
---

作为 IT 运维人员，每天要处理大量的 OAK 工单。手动处理效率太低，容易出错，于是开发了 Smart OAK 自动化工具来提升工作效率。

## 背景

Oak 是我们公司用的工单系统，日常工作包括：

- 📧 **批量邮件发送** - 根据工单号给不同支持人员发送邮件通知
- 🔍 **密码到期查询** - 查看工单的工单密码什么时候过期
- 🌐 **自动打开工单** - 直接在浏览器中打开工单链接
- 📊 **状态跟踪** - 跟踪哪些工单已处理，哪些还未处理

手动处理这些问题非常耗时，而且容易出错。于是用 Python 开发了自动化工具。

## 核心功能

### 1. 批量邮件发送

工具内置邮件模板系统，可以根据工单类型选择不同的邮件模板：

```python
# 支持多种邮件模板
templates = [
    "系统升级通知",
    "服务请求确认",
    "问题反馈",
    "日常巡检报告"
]

# 批量发送
for ticket in tickets:
    send_email(ticket, template)
```

### 2. OAK 密码到期查询

自动计算密码剩余天数，并高亮显示即将过期的工单：

```python
def calculate_pw_days_left(ticket_no):
    """计算密码剩余天数"""
    pw_expiry = get_expiry_date(ticket_no)
    days_left = (pw_expiry - datetime.now()).days
    return days_left
```

### 3. 自动打开工单链接

使用 Playwright 自动打开工单页面，支持无头模式运行：

```python
from playwright.sync_api import sync_playwright

async def open_ticket(ticket_no):
    """打开工单页面"""
    browser = await sync_playwright().launch(headless=True)
    page = await browser.new_page()
    await page.goto(OAK_TASK_URL % ticket_no)
    await browser.close()
```

### 4. Excel 数据导入

支持从 Excel 文件导入工单列表，自动识别列名并映射：

```python
def load_tickets_from_excel(file_path):
    """从 Excel 加载工单数据"""
    df = pd.read_excel(file_path)
    # 自动识别列名映射
    # ...
    return df
```

## 技术亮点

### 多线程处理

使用线程队列避免 UI 阻塞：

```python
import threading
import queue

task_queue = queue.Queue()
worker_thread = threading.Thread(target=worker, args=(task_queue,))
worker_thread.start()
```

### 无头浏览器集成

使用 Playwright 的无头模式，不弹出浏览器窗口，提高执行速度：

```python
browser = await sync_playwright().launch(headless=True)
```

### 剪贴板功能

自动复制工单号到剪贴板，方便快速粘贴：

```python
import pyperclip

pyperclip.copy(ticket_no)
```

## 界面设计

采用紧凑型界面设计，提升信息密度：

```
┌─────────────────────────────────────────┐
│  [模板选择]                                  │
├─────────────────────────────────────────┤
│  工单列表                    │  工单详情  │
│  [表格区域]                  │  [描述]    │
│                                │  [操作]    │
│                                │            │
└─────────────────────────────────────────┘
```

**设计原则：**
- 窗口可调，非全屏也能完整显示
- 行高紧凑（22px），可显示更多数据
- 按钮统一高度，保持界面整洁

## 使用方法

### 基本操作

1. **选择邮件模板** - 从下拉列表选择
2. **导入 Excel** - 或手动输入工单号
3. **批量发送** - 一键发送所有工单
4. **查看日志** - 底部日志区显示操作记录

### 配置说明

编辑脚本中的常量配置：

```python
# OAK 工单链接
OAK_TASK_URL = "https://oak-agent.cn.kworld.kpmg.com/#/sd/agent/processticket?TicketNo=%s"

# 邮件模板路径
EMAIL_TEMPLATE_PATH = r"C:\Temp\PythonScript\Email_Template"

# Excel 文件列名映射
COL_TICKET = "Ticket no"
COL_REQUESTOR = "Requestor"
COL_SERVICE_INFO = "Service information"
```

## 遇到的问题

### 1. Playwright 安装问题

**问题**：Playwright 需要下载浏览器驱动，首次安装较慢

**解决**：使用 `playwright install` 预先下载驱动

```bash
pip install playwright
playwright install
```

### 2. 编码问题

**问题**：处理中文内容时可能出现编码错误

**解决**：统一使用 UTF-8 编码

```python
# -*- coding: utf-8 -*-
import pandas as pd
df = pd.read_excel(file_path, encoding='utf-8')
```

### 3. UI 线程问题

**问题**：长时间运行可能导致界面无响应

**解决**：使用多线程处理任务，主线程保持响应

```python
import threading

def long_running_task():
    # 耗时任务
    pass

thread = threading.Thread(target=long_running_task)
thread.start()
```

## 效果评估

使用前（手动）：
- 处理 10 个工单：约 15-20 分钟
- 容易出错：人工复制工单号、选错模板等

使用后（自动化）：
- 处理 10 个工单：约 2-3 分钟
- 准确率高：自动识别列名、自动计算剩余天数
- 可追溯：完整日志记录

**效率提升：约 5-10 倍**

## 后续改进方向

- [ ] 添加定时提醒功能（密码过期前自动提醒）
- [ ] 支持更多邮件模板
- [ ] 添加工单状态同步功能
- [ ] 集成到公司统一平台
- [ ] 添加数据统计和报表功能

## 总结

Smart OAK 工具解决了 IT 运维中的重复性工作，通过自动化大幅提升了工作效率。

**核心价值：**
- ✅ 减少重复劳动
- ✅ 提升响应速度
- ✅ 降低出错率
- ✅ 规范化操作流程

技术栈：Python + Tkinter + Playwright + Pandas

---

**项目版本**：v2.6.1
**最后更新**：2026-02-10
