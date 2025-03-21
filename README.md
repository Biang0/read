# 📖 wxread

**本项目基于 [findmover/wxread](https://github.com/findmover/wxread) 进行优化和扩展。**  
使用 **GitHub Actions** 自动执行 **微信阅读任务**，支持 **定时触发** 和 **手动触发**，可自由配置 **阅读时间** 和 **执行延迟**。  

自动保持 GitHub 提交状态常绿。
> a commit a day keeps your girlfriend away.
---

## 🚀 功能介绍  

✅ **自动模式**：根据 **定时任务规则** 计算 **READ_NUM**，并 **随机延迟 0-20 分钟**（若手动触发但未设置自定义延迟，也会有随机延迟）。  

✅ **手动模式**：  
   - **默认生成** `60-120`（等效 **30-60 分钟**）的随机阅读时间。  
   - **支持自定义** `custom_time` 设定阅读时间，并计算 **READ_NUM = custom_time * 2**。  

✅ **自定义延迟**：手动触发时，可使用 `custom_delay` **手动设置任务执行的延迟时间**（单位：分钟）。  

✅ **智能计算 READ_NUM**：
   - **自动模式**：基于定时规则生成合适的阅读时间。  
   - **手动模式**：支持 `custom_time` 设定阅读时长，或使用系统默认值。  
   - **自定义 `custom_delay`** 控制任务启动时间。  

---

## ⏰ 触发方式  

本项目支持 **GitHub Actions 触发**，包括 **定时触发** 和 **手动触发**，可根据需求进行配置。  

### 1️⃣ **自动触发（定时任务）**  

- **定时任务触发时，READ_NUM 自动生成**，无需手动设置。  
- **若手动触发但未设置 `custom_delay`**，则会 **随机延迟 0-20 分钟**，防止固定时间触发导致异常。  
- **可使用 `custom_delay` 自定义具体延迟时间**，确保任务在指定时间运行。  

**默认定时任务时间（北京时间 UTC+8）**：  

| 任务类型 | 触发时间（北京时间） | UTC 时间 |
|----------|------------------|---------|
| 晚间任务 | 每天 22:00       | 14:00   |
| 早间任务 | 周一至周五 05:00 | 前一天 21:00 |
| 午间任务 | 周一至周五 11:40 | 03:40   |
| 周末任务 | 周六 & 周日 17:00 | 09:00   |
| 随机任务 | 周六 & 周日 08:00 | 前一天 00:00 |

### 2️⃣ **手动触发（GitHub Actions 触发）**  

可通过 **GitHub Actions** 手动触发阅读任务，并自由配置 **阅读时间** 和 **延迟**。  

```yaml
workflow_dispatch:
  description: 手动触发vx阅读任务
  inputs:
    mode:
      type: choice
      description: |
        选择运行模式：
        - **自动**：按定时规则计算 `READ_NUM`，默认 `0-20` 分钟随机延迟（可自定义 `custom_time` 和 `custom_delay`）。
        - **手动**：`READ_NUM` 默认为 `60-120`，可自定义 `custom_time`，默认无延迟（可自定义 `custom_delay`）。
      required: true
      default: '手动'
      options:
        - 自动
        - 手动
    custom_time:
      type: string
      description: |
        - **自动模式**：若填写 `custom_time`，则 `READ_NUM = custom_time * 2`，默认 `0-20` 分钟随机延迟（可用 `custom_delay` 设定具体值）。
        - **手动模式**：若填写 `custom_time`，则 `READ_NUM = custom_time * 2`，默认无延迟（可用 `custom_delay` 设定具体值）。
      required: false
    custom_delay:
      type: string
      description: |
        - **自动模式**：若填写 `custom_delay`，覆盖 `0-20` 分钟的默认随机延迟，任务将在 `custom_delay` 分钟后执行。
        - **手动模式**：若填写 `custom_delay`，任务将在 `custom_delay` 分钟后执行。
      required: false
```

---

## ⚠️ 处罚介绍

请注意，使用自动化工具进行阅读可能违反平台的相关规则。如果被检测到，可能会受到以下处罚：  

1. **阅读时长不计入总时长**：系统可能会判定异常行为，使阅读时间不被计入有效阅读时长。  
2. **账号限制**：可能会出现账号阅读权限被限制的情况，影响后续正常使用。  
3. **封号风险**：如被多次检测到违规操作，严重情况下可能导致账号封禁。  
4. **设备异常警告**：如果系统检测到短时间内高频率的阅读行为，可能会触发设备异常提醒或验证码验证。  
5. **任务执行失败**：如服务器检测到异常活动，可能会导致任务无法正常执行。  

**降低风险的建议**：  
- **避免高频触发任务**：合理安排执行时间，避免过于频繁地运行任务。  
- **阅读时间保持合理**：设定 `READ_NUM` 在 **正常阅读时长范围内**，不要设定过高的值。  
- **定期检查账号状态**：留意是否收到异常提醒，防止账户受限。  
- **使用多种触发模式**：结合自动模式与手动模式，避免固定时间触发导致异常。  

---

## 🚀 **执行流程**  

### **GitHub Actions 自动执行**  
1. **定时任务** 会在设定时间自动触发，无需手动干预。  
2. **手动模式** 可在 GitHub Actions 界面手动触发，并自由设定 `custom_time` 和 `custom_delay`。  
3. **READ_NUM** 计算方式基于 **系统自动计算** 或 **用户自定义输入**。  
4. **执行 `main.py`** 完成阅读任务。  

### **本地运行**  
如需在本地运行，请手动执行：
```bash
python main.py  
```  

---

## 🔧 **环境变量（Secrets）**  

**⚠️ 请在 GitHub Secrets 中配置 `WXREAD_CURL_BASH`，否则无法正常运行。**  

| 变量名 | 说明 | 必填 |
|--------|------|------|
| `WXREAD_CURL_BASH` | 微信阅读 API 的 curl_bash 数据 | ✅ |
| `PUSH_METHOD` | 推送方式（pushplus / wxpusher / telegram） | ❌ |
| `PUSHPLUS_TOKEN` | pushplus 推送 Token（若使用 pushplus） | ❌ |
| `WXPUSHER_SPT` | wxpusher 推送 Token（若使用 wxpusher） | ❌ |
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token（若使用 Telegram） | ❌ |
| `TELEGRAM_CHAT_ID` | Telegram Chat ID（若使用 Telegram） | ❌ |
