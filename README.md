ql-docker.py 可以单下载直接上传到你armv8机子上 运行（我是n1盒子刷了软路由成功运行）这个自动利用F2a  arm32位没弄了手上没有32位的机子了 玩客云

ql-docker-plus.py  这个能看到款额

青龙面板使用

注意：如果是docker容器创建的青龙，请使用whyour/qinglong:debian镜像，latest（alpine）版本可能无法安装部分依赖

依赖安装

安装Python依赖

进入青龙面板 -> 依赖管理 -> 安装依赖

依赖类型选择python3

自动拆分选择是
``
selenium
pyotp
requests
loguru
``

点击确定

安装 linux chromium 依赖

青龙面板 -> 依赖管理 -> 安装Linux依赖

名称填 chromium

若安装失败，可能需要执行apt update更新索引（若使用docker则需进入docker容器执行）

添加仓库

进入青龙面板 -> 订阅管理 -> 创建订阅

依次在对应的字段填入内容（未提及的不填）：

名称：clawcloud 登陆

类型：公开仓库

链接：https://github.com/djkyc/clawcloud-auto-login.git

分支：main

定时类型：crontab

定时规则(拉取上游代码的时间，每六小时一次，可以自由调整频率): 0 */6 * * *

配置环境变量

进入青龙面板 -> 环境变量 -> 创建变量

环境变量配置完成!

🎯 配置方式

在青龙面板的环境变量中添加:

变量名	值	说明  

CLAW_ACCOUNTS	账号1----密码1----2FA密钥1&账号2----密码2----2FA密钥2	多账号配置

TG_BOT_TOKEN	your_token	Telegram Bot Token

TG_CHAT_ID	your_chat_id	Telegram Chat ID

CLAW_CLOUD_URL	https://eu-central-1.run.claw.cloud	可选,默认欧洲区

📝 配置格式

CLAW_ACCOUNTS=user1@gmail.com----pass123----SECRET1&user2@gmail.com----pass456----SECRET2

格式说明:

每个账号: 用户名----密码----2FA密钥(可选)

多个账号用 & 分隔

2FA 密钥可以留空

🎯 配置示例

2个账号,都有 2FA:

user1@gmail.com----password123----JBSWY3DPEHPK3PXP&user2@gmail.com----password456----ABCDEFGHIJKLMNOP

2个账号,只有第1个有 2FA:

user1@gmail.com----password123----JBSWY3DPEHPK3PXP&user2@gmail.com----password456

3个账号,都没有 2FA:

user1@gmail.com----password123&user2@gmail.com----password456&user3@gmail.com----password789

🔧 青龙面板操作步骤

进入青龙面板 → 环境变量

点击 添加变量

名称: CLAW_ACCOUNTS

值: 按格式填写

点击 确定
✅ 优点
✅ 密码不在脚本中明文存储
✅ 便于管理和修改
✅ 支持任意数量账号
✅ 安全性更高
📊 预期日志
从环境变量 CLAW_ACCOUNTS 加载账号配置
加载账号: user1@gmail.com
加载账号: user2@gmail.com
📊 共配置 2 个账号
详细配置指南请查看文档 👇
手动拉取脚本

首次添加仓库后不会立即拉取脚本，需要等待到定时任务触发，当然可以手动触发拉取
点击右侧"运行"按钮可手动执行
运行结果
青龙面板中查看
进入青龙面板 -> 定时任务 -> 找到 签到 -> 点击右侧的日志

方法 1: 在青龙面板中安装(推荐)  

进入青龙面板

点击 依赖管理

选择 Python3 标签

在输入框中输入: pyotp

点击 安装

方法 2: SSH 进入容器安装


# SSH 连接到服务器
ssh root@你的服务器IP

# 进入青龙容器
docker exec -it qinglong bash

# 安装 pyotp
pip3 install pyotp

# 退出容器
exit

 
 一次性安装所有依赖
 
在青龙面板的 依赖管理 → Python3 中,依次安装:

selenium
pyotp
requests
loguru

🎯 青龙面板定时任务设置

进入青龙面板 → 定时任务

点击 添加任务

填写:

名称: ClawCloud 自动登录

命令: task clawcloudrunifo.py

定时规则: 30 8 * * * (每天 8:30)

或者: 0 */6 * * * (每 6 小时)

点击 确定
<img width="527" height="447" alt="image" src="https://github.com/user-attachments/assets/b429dc14-8097-4a3e-aa14-0fe1f365cbc7" />

---

# ☁️ ClawCloud Auto-Login / 自动保活V1.1版

此工作流旨在实现 **每 15 天自动登录一次 ClawCloud (爪云)** 以保持账号活跃。

为了确保自动化脚本顺利运行，**必须**满足以下两个前置条件：

1. ❌ **关闭 Passkey (通行密钥)**：避免脚本无法处理生物识别弹窗。
2. 
3.  ✅ **开启 2FA (双重验证)**：配合脚本中的 PyOTP 自动生成验证码，绕过异地登录风控。
4.  `增加TG接收消息。`
---

## 🛠️ 配置步骤

### 第一步：Fork 本项目
点击页面右上角的 **Fork** 按钮，将此仓库复制到您的 GitHub 账号下。

### 第二步：开启 GitHub 2FA 并获取密钥
脚本需要通过 2FA 密钥自动计算验证码，因此不能只扫二维码，必须获取**文本密钥**。

1. 登录 GitHub，点击右上角头像 -> **Settings**。
2. 在左侧菜单选择 **Password and authentication**。
3. 找到 "Two-factor authentication" 区域，点击 **Enable 2FA**。
4. 选择 **Set up using an app**。
5. **⚠️ 关键步骤**：
   > 页面显示二维码时，**不要直接点击 Continue**。请点击二维码下方的蓝色小字 **"Setup Key"**（或 "enter this text code"）。
6. **复制显示的字符串密钥**（通常是 16 位字母数字组合）。
   * *注意：同时也请用手机验证器 App (如 Google Auth) 扫描二维码或输入密钥，以完成 GitHub 的验证流程。*

7. **⚠️ 记得把Preferred 2FA method选为Authenticator App，否则脚本不生效**
### 第三步：配置 GitHub Secrets
为了保护您的账号安全，请将敏感信息存储在仓库的 Secrets 中。

1. 进入您的 GitHub 仓库页面。
2. 依次点击导航栏的 **Settings** -> 左侧栏 **Secrets and variables** -> **Actions**。
3. 点击右上角的 **New repository secret** 按钮。
4. 依次添加以下 3 个 Secret：

| Secret 名称 | 填入内容 (Value) | 说明 |
| :--- | :--- | :--- |
| `GH_USERNAME` | **您的 GitHub 账号** | 通常是您的登录邮箱 |
| `GH_PASSWORD` | **您的 GitHub 密码** | 登录用的密码 |
| `GH_2FA_SECRET` | **2FA 密钥** | 第二步中复制的那串字符 (请去除空格) |
| `TG_BOT_TOKEN` | **机器人的token**| 
| `TG_CHAT_ID` | **机器人的id**| 
### 第四步：启用工作流权限 (⚠️ 重要)
由于是 Fork 的仓库，GitHub 默认可能会禁用 Actions 以防止滥用。

1. 点击仓库顶部的 **Actions** 选项卡。
2. 如果看到警告提示，请点击绿色的 **"I understand my workflows, go ahead and enable them"** 按钮。

### 第五步：手动测试运行
配置完成后，建议手动触发一次以确保一切正常。

1. 点击 **Actions** 选项卡。
2. 在左侧列表中选择 **ClawCloud Run Auto Login**。
3. 点击右侧的 **Run workflow** 下拉菜单 -> 点击绿色 **Run workflow** 按钮。
4. 等待运行完成，查看日志确保显示 `🎉 登录成功`。

---
**✅ 完成！之后脚本将每隔 15 天自动执行一次保活任务。**
# 项目名

<!-- 徽章区 -->
[![Stars](https://img.shields.io/github/stars/djkyc/clawcloud-auto-login)](https://github.com/djkyc/clawcloud-auto-login)

<!-- Star History -->
## ⭐ Star History
[![Star History Chart](https://api.star-history.com/svg?repos=djkyc/clawcloud-auto-login&type=Date)](https://star-history.com/#djkyc/clawcloud-auto-login&Date)


