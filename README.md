---
title: "Deploying OmniTools on Cloudflare Pages: Complete Bilingual Setup Guide | 在 Cloudflare Pages 上部署 OmniTools 完整双语教程"
date: 2026-10-04
draft: false
tags: ["Cloudflare", "OmniTools", "Homelab", "Vite", "React", "DevOps", "Bilingual"]
summary: "A battle-tested, step-by-step bilingual guide to deploying OmniTools on Cloudflare Pages under omni.itexpatchina.com, including real-world troubleshooting solutions for SPA routing, git remote permissions, and pnpm lockfile errors."
---

# Deploying OmniTools on Cloudflare Pages: Complete Bilingual Guide
# 在 Cloudflare Pages 上部署 OmniTools 完整双语教程

This guide provides a production-ready, step-by-step walkthrough to host **[OmniTools](https://github.com/iib0011/omni-tools)** — an open-source, privacy-first web utility suite — on **Cloudflare Pages** under a custom subdomain (`omni.itexpatchina.com`). It includes every command, setting, and real-world fix encountered during successful deployment.

本教程提供了一份完整的实战指南，带你将开源隐私工具箱 **OmniTools** 部署到 **Cloudflare Pages** 并绑定自定义子域名 (`omni.itexpatchina.com`)。教程中包含了部署过程中遇到的所有实际问题（SPA 路由、Git 权限、pnpm 锁定文件报错）及其完美解决方案。

---

## 📌 Architecture & Prerequisites / 架构说明与前置准备

### English
* **Upstream App:** `https://github.com/iib0011/omni-tools.git`
* **Personal GitHub Repo:** `https://github.com/itexpatchina/omni-tools.git`
* **Tech Stack:** React + TypeScript + Material UI + Vite (Client-side WASM SPA)
* **Custom Subdomain:** `https://omni.itexpatchina.com/`
* **Build Characteristics:** Static SPA built into `dist/`, zero backend or database required.

### 中文
* **上游开源仓库：** `https://github.com/iib0011/omni-tools.git`
* **个人 GitHub 仓库：** `https://github.com/itexpatchina/omni-tools.git`
* **技术栈：** React + TypeScript + Material UI + Vite (纯前端 WASM 单页应用)
* **自定义子域名：** `https://omni.itexpatchina.com/`
* **构建特性：** 静态 SPA 输出至 `dist/` 目录，无需任何后端服务器或数据库。

---

## 🛠️ Step-by-Step Deployment Guide / 逐步部署指南

### Step 1: Create an Empty Repository on GitHub / 第一步：在 GitHub 创建空仓库

#### English
1. Go to **[github.com/new](https://github.com/new)**.
2. Set **Repository name** to `omni-tools`.
3. Set visibility to **Public**.
4. **Uncheck** all initialization options (Do NOT add README, `.gitignore`, or License).
5. Click **Create repository**.

#### 中文
1. 打开 **[github.com/new](https://github.com/new)**。
2. 仓库名称 (Repository name) 填写 `omni-tools`。
3. 可见性设置为 **Public**。
4. **取消勾选** 所有初始化选项（不要勾选 README、`.gitignore` 或 License）。
5. 点击 **Create repository**。

---

### Step 2: Clone Upstream, Add SPA Redirect & Fix Remote URL / 第二步：克隆源码、配置 SPA 重定向与修改远程地址

#### English
When cloning the upstream repository, pushing directly will cause a `403 Permission denied` error because Git points to the author's original repository. You must update the remote URL to point to your own GitHub account.

Additionally, to prevent `404 Not Found` errors when refreshing deep links in client-side routed SPAs, add a `_redirects` rule file in the `public/` folder.

Run the following commands in PowerShell:

```powershell
# Navigate to working directory
cd C:\Users\MBALOCAL

# 1. Clone original repo
git clone https://github.com/iib0011/omni-tools.git
cd omni-tools

# 2. Add Cloudflare Pages SPA redirect rule
Set-Content -Path public\_redirects -Value "/* /index.html 200"

# 3. Fix 403 Permission error by pointing remote origin to your GitHub account
git remote set-url origin https://github.com/itexpatchina/omni-tools.git
```

#### 中文
由于直接从原作者仓库克隆，如果不修改远程仓库地址，推送时会出现 `403 Permission denied` 权限错误。你需要将远程仓库地址更新为自己的 GitHub 仓库。

此外，由于 OmniTools 是前端单页应用（SPA），需要在 `public/` 目录下添加 `_redirects` 重定向文件，防止用户刷新子页面（如 `/tool/image/convert`）时出现 404 错误。

在 PowerShell 中运行以下命令：

```powershell
# 进入工作目录
cd C:\Users\MBALOCAL

# 1. 克隆原作者仓库
git clone https://github.com/iib0011/omni-tools.git
cd omni-tools

# 2. 添加 Cloudflare Pages 静态重定向规则
Set-Content -Path public\_redirects -Value "/* /index.html 200"

# 3. 将 remote origin 修改为你的个人 GitHub 仓库地址，解决 403 报错
git remote set-url origin https://github.com/itexpatchina/omni-tools.git
```

---

### Step 3: Fix `pnpm-lock.yaml` CI Build Error / 第三步：修复 `pnpm-lock.yaml` CI 构建报错

#### English
During Cloudflare Pages automated builds, `pnpm` will enforce strict lockfile checks (`ERR_PNPM_OUTDATED_LOCKFILE`) if `pnpm-lock.yaml` is out of sync with `package.json`.

**Solution:** Remove `pnpm-lock.yaml` from the repository so Cloudflare Pages falls back to standard `npm install`, which installs cleanly without errors.

```powershell
# Remove outdated lockfile
Remove-Item pnpm-lock.yaml

# Stage changes, commit and push to your personal GitHub repository
git add public/_redirects
git rm pnpm-lock.yaml
git commit -m "fix: add SPA redirect and remove outdated pnpm lockfile"
git push -u origin main
```

#### 中文
在 Cloudflare Pages 的自动构建过程中，如果 `pnpm-lock.yaml` 与 `package.json` 版本不完全匹配，`pnpm` 会抛出 `ERR_PNPM_OUTDATED_LOCKFILE` 错误并终止构建。

**解决办法：** 从仓库中删除 `pnpm-lock.yaml`，促使 Cloudflare Pages 回退使用标准的 `npm install`，从而实现无错安装和成功构建。

```powershell
# 删除不兼容的 lockfile
Remove-Item pnpm-lock.yaml

# 暂存更改、提交并推送到你的个人 GitHub 仓库
git add public/_redirects
git rm pnpm-lock.yaml
git commit -m "fix: add SPA redirect and remove outdated pnpm lockfile"
git push -u origin main
```

---

### Step 4: Configure & Deploy on Cloudflare Pages / 第四步：配置并部署 Cloudflare Pages

#### English
1. Log in to **[Cloudflare Dashboard](https://dash.cloudflare.com/)**.
2. Go to **Workers & Pages** → **Create Application** → **Pages** tab → **Connect to Git**.
3. Select `itexpatchina/omni-tools` and click **Begin setup**.
4. Configure build settings:
   * **Framework Preset:** `Vite` (or `None` — do **NOT** choose *VitePress*)
   * **Build Command:** `npm run build`
   * **Build Output Directory:** `dist`
5. Click **Save and Deploy**.

#### 中文
1. 登录 **[Cloudflare Dashboard](https://dash.cloudflare.com/)**。
2. 依次进入 **Workers & Pages** → **Create Application** → 选择 **Pages** 标签页 → **Connect to Git**。
3. 选择你的仓库 `itexpatchina/omni-tools`，点击 **Begin setup**。
4. 设置构建参数：
   * **Framework Preset（框架预设）：** 选择 `Vite` 或 `None`（**切勿**选择 *VitePress*，两者不同）
   * **Build Command（构建命令）：** `npm run build`
   * **Build Output Directory（输出目录）：** `dist`
5. 点击 **Save and Deploy**。

---

### Step 5: Map Custom Subdomain (`omni.itexpatchina.com`) / 第五步：绑定自定义子域名 (`omni.itexpatchina.com`)

#### English
1. Inside your deployed project in Cloudflare Pages, navigate to the **Custom domains** tab.
2. Click **Set up a custom domain**.
3. Type **`omni.itexpatchina.com`** and click **Continue**.
4. Click **Activate domain**. Cloudflare will configure the DNS CNAME record and issue an SSL certificate automatically within 1–2 minutes.

#### 中文
1. 在 Cloudflare Pages 项目页面中，点击 **Custom domains** 标签页。
2. 点击 **Set up a custom domain**。
3. 输入 **`omni.itexpatchina.com`** 并点击 **Continue**。
4. 点击 **Activate domain**。Cloudflare 将自动配置 CNAME 解析并生成 SSL 证书（约需 1–2 分钟）。

---

## 🔍 Troubleshooting & Verification / 常见排错与验证

| Issue / 现象 | Cause / 原因 | Solution / 解决办法 |
| :--- | :--- | :--- |
| `403 Permission denied` on `git push` | Git is pointing to `iib0011/omni-tools` | Run `git remote set-url origin https://github.com/itexpatchina/omni-tools.git` |
| `ERR_PNPM_OUTDATED_LOCKFILE` | `pnpm-lock.yaml` out of sync in CI | Run `Remove-Item pnpm-lock.yaml`, commit and push |
| 404 Error when refreshing inner pages | Missing SPA routing rules | Create `public/_redirects` containing `/* /index.html 200` |
| Build output directory error | Selected `VitePress` instead of `Vite` | Set output directory explicitly to `dist` |

---

## 🎉 Conclusion / 总结

Your OmniTools application is now globally cached on Cloudflare's edge network at **`https://omni.itexpatchina.com`** with automatic HTTPS, zero server costs, and instant CI/CD deployment on every `git push`!

你的 OmniTools 工具箱现已成功在 Cloudflare 全球边缘网络上线（**`https://omni.itexpatchina.com`**），具备全自动 HTTPS 证书、零服务器成本，且未来每次 `git push` 均会自动触发无缝持续集成部署！
