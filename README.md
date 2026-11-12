# TraffMonetizer CLI_V2 镜像同步 Action
> GitHub Action 自动同步 `traffmonetizer/cli_v2` 多架构镜像到你自己的 Docker Hub 仓库，支持 amd64 / armv7 / arm64。
> 定时自动校验更新，**手动触发时强制重新同步**，fork 后简单配置即可使用。

## ✨ 功能特性
- ✅ 上游源：`traffmonetizer/cli_v2`（官方多架构镜像）
- ✅ 支持架构：`linux/amd64`、`linux/arm/v7`、`linux/arm64/v8`
- ✅ 定时任务：每日 UTC 17:00（北京时间 01:00）自动对比 digest，有更新才推送
- ✅ 手动触发：点击 Run workflow 强制同步，不管镜像是否更新
- ✅ 基于 buildx imagetools 清单复制，**不下载镜像到本地 Runner**，规避架构拉取报错
- ✅ 自动合并多架构 manifest 清单，`latest` tag 可以在不同架构设备自动拉取对应版本

## 📋 前置准备
1. 一个 Docker Hub 账号
2. 提前在 Docker Hub 创建一个**空仓库**（例如 `yourname/traffmonetizer-cli`）
3. 生成 Docker Hub Access Token：
   - 地址：https://hub.docker.com/settings/security
   - 权限：勾选 `read, write, delete`
4. Fork 本仓库到你自己的 GitHub

## 🔐 GitHub Secrets 配置（必须！）
进入你 Fork 后的仓库 → Settings → Secrets and variables → Actions → New repository secret

| Secret 名称 | 填写内容 |
| ---- | ---- |
| `DOCKERHUB_USERNAME` | 你的 Docker Hub 用户名 |
| `DOCKERHUB_TOKEN` | Docker Hub Access Token |
| `DOCKERHUB_REPO` | 你的仓库全称，示例：`yourname/traffmonetizer-cli` |

> ⚠️ 注意：不要加 tag，只填 `用户名/仓库名`

## 🚀 使用方式
### 方式1：手动触发（强制同步）
Actions → 选择 `Sync TraffMonetizer CLI_V2 Multi-arch Image` → 点击 `Run workflow`
> 手动触发会无视 digest 对比，直接复制并重建 manifest，适合上游更新后立刻同步。

### 方式2：自动定时
默认配置：`0 17 * * *`（UTC 17点 = 北京时间凌晨 01:00）
定时模式下：会先对比上游和本地镜像 digest，**只有上游变更才推送**，减少 Docker Hub 流量。

## 📌 镜像标签说明
同步完成后，你的 Docker Hub 仓库会有 4 个 tag：
- `latest`：多架构清单（推荐直接使用）
- `amd64`：x86_64 架构
- `armv7`：arm32（例如 32位树莓派）
- `arm64`：arm64 / aarch64（树莓派4、N1、香橙派等64位ARM）

### 拉取示例
```bash
# 自动匹配架构
docker run -d --name traffmonetizer yourname/traffmonetizer-cli:latest

# 固定拉取 amd64
docker run -d yourname/traffmonetizer-cli:amd64
