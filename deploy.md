# PixelStock 部署指南

## 项目结构
```
pixelstock/
├── index.html          # 单页应用主文件
├── deploy.md           # 本指南
└── README.md           # 项目说明（可选）
```

## 部署方式

### 方案一：静态托管（推荐）
1. **GitHub Pages**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/你的用户名/pixelstock.git
   git push -u origin main
   ```
   - 在仓库设置中启用 GitHub Pages，选择 `main` 分支根目录
   - 访问地址：`https://你的用户名.github.io/pixelstock`

2. **Netlify**
   - 拖拽 `index.html` 到 Netlify 上传区域
   - 或连接 GitHub 仓库自动部署
   - 支持自定义域名、HTTPS、自动部署

3. **Vercel**
   ```bash
   npm i -g vercel
   vercel
   ```
   - 支持边缘网络、自动 HTTPS

### 方案二：云服务器（Nginx）
```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    root /var/www/pixelstock;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 启用 gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
}
```
部署步骤：
```bash
# 上传文件
scp -r output/* user@server:/var/www/pixelstock/

# 设置权限
sudo chown -R www-data:www-data /var/www/pixelstock
sudo chmod -R 755 /var/www/pixelstock
```

### 方案三：Docker 容器化
```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
EXPOSE 80
```
构建运行：
```bash
docker build -t pixelstock .
docker run -d -p 8080:80 pixelstock
```

## 域名配置
1. 购买域名（如 pixelstock.com）
2. 在 DNS 管理中添加记录：
   ```
   A 记录 @ → 服务器 IP
   CNAME www → your-domain.com
   ```
3. 申请 SSL 证书（Let's Encrypt）：
   ```bash
   certbot --nginx -d your-domain.com -d www.your-domain.com
   ```

## 环境要求
- 现代浏览器（Chrome 90+, Firefox 88+, Safari 14+）
- 支持 ES6+ 和 CSS3
- 无需后端服务（纯静态）

## 性能优化建议
1. 启用 HTTP/2
2. 配置浏览器缓存：
   ```nginx
   location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
       expires 1y;
       add_header Cache-Control "public, immutable";
   }
   ```
3. 使用 CDN 加速静态资源

## 监控与维护
- 使用 Google Analytics 或 Umami 统计访问
- 定期备份网站文件
- 监控 SSL 证书过期时间