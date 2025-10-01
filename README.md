# south-api 部署说明

该目录包含：
- `app.py` 你的 Flask 应用（入口对象为 `app`）
- `requirements.txt` 依赖
- `Procfile` 适用于 Railway/Render/Heroku 的启动命令
- `Dockerfile` 便于自建主机或容器平台部署
- `Caddyfile` + `docker-compose.yml` 自建服务器自动签发 SSL（Let's Encrypt）方案

## 方案 A：Railway（最快捷）

1. 推送到 GitHub：
   ```bash
   cd south-api-deploy
   git init
   git add .
   git commit -m "deploy: initial"
   git branch -M main
   git remote add origin https://github.com/<your-user>/south-api.git
   git push -u origin main
   ```

2. 打开 <https://railway.app> → New Project → Deploy from GitHub → 选择仓库
3. 部署后在 **Settings → Generate Domain** 获取公网地址（自动 HTTPS）。

> Railway 会注入 `$PORT`，已在 `Procfile` 处理，无需修改端口。

## 方案 B：Render

1. 同样推送到 GitHub。
2. <https://render.com> → New → Web Service → 选择仓库
   - Start Command：`gunicorn app:app --bind 0.0.0.0:$PORT --workers 2`

## 方案 C：自建服务器（Docker + Caddy）

服务器需 Ubuntu 20.04+/22.04+，域名已解析到服务器 IP。

```bash
# 1) 安装 Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
# 重新登录终端使之生效

# 2) 上传该目录（或在服务器 git clone）
# 假设目录名为 south-api-deploy
cd south-api-deploy

# 3) 启动（自动获取 HTTPS 证书）
docker compose up -d --build
```

访问 `https://example.com`（把 Caddyfile 中的域名改为你的域名）。

## 常见问题
- 入口需是 `app.py` 中的 `app`：已满足。
- 第三方依赖需在 `requirements.txt`：已包含 `Flask`, `requests`, `gunicorn`。
- 生产环境请勿使用 `app.run()` 开发服务器，已改用 `gunicorn`。

