# PD-build

TalkyTimes 公开编译 CI。**本仓库不含 TalkyTimes 源码**，仅包含 GitHub Actions workflow。

## 工作原理

1. 私有仓库 [`crazylin/PD`](https://github.com/crazylin/PD) 推送 `v*` tag 时，触发 `repository_dispatch` 到本仓库。
2. 本 workflow 使用 PAT 克隆私有仓库指定 tag，在 GitHub 托管 runner 上编译完整安装包。
3. 产物上传 Cloudflare R2（`TalkyTimes/{version}/`），并同步许可后台 URL 与 sha256。

## 手动触发

Actions → **Build TalkyTimes release** → **Run workflow**，输入 tag（如 `v0.1.0`）。

## Secrets（Settings → Secrets and variables → Actions）

| Secret | 说明 |
|--------|------|
| `PD_CLONE_PAT` | 读取私有 `crazylin/PD`（`contents:read`） |
| `R2_ACCESS_KEY_ID` | Cloudflare R2 访问密钥 |
| `R2_SECRET_ACCESS_KEY` | Cloudflare R2 密钥 |
| `R2_ENDPOINT` | R2 S3 兼容 endpoint |
| `R2_BUCKET` | R2 bucket 名称 |
| `R2_DOWNLOAD_BASE` | 公开下载 URL 前缀（HTTPS） |
| `TALKY_LICENSE_SERVER_URL` | 许可 API 基址 |
| `TALKY_LICENSE_DEPLOY_TOKEN` 或 `TALKY_LICENSE_ADMIN_TOKEN` | 许可后台写入 token |

## PAT 权限

### PD-build：`PD_CLONE_PAT`

- Resource owner: `crazylin`
- Repository access: 仅 `PD`（私有）
- Permissions: **Contents → Read-only**

### 私有 PD：`PD_BUILD_DISPATCH_TOKEN`

- Repository access: 仅 `PD-build`
- Permissions: **Contents → Read-only**, **Actions → Read and write**（或 repo scope 含 workflow）
