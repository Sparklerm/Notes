```tsx
# 安装依赖
npm i vite-plugin-compression -D

# vite.config.js 文件中配置
import viteCompression from 'vite-plugin-compression'

export default defineConfig({
  ......
  plugins: [
	......
  viteCompression({
    verbose: true,
    disable: false,
    threshold: 10240,
    algorithm: 'gzip',
    ext: '.gz',
  }),
  ],
  ......
});

```

```
# 注意还需要在服务器上设置gzip，否则不会请求
gzip on;
gzip_vary on;
gzip_proxied any;
 gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types image/svg+xml text/plain text/html text/xml text/css text/javascript application/xml application/xhtml+xml application/rss+xml application/javascript application/x-javascript application/x-font-ttf application/vnd.ms-fontobject font/opentype font/ttf font/eot font/otf;
```

