# Hexo
- [官网](https://hexo.io/zh-cn/)

### 配域名
cd 你的hexo本地目录, cd source, touch CNAME创建CNAME文件, open CNAME打开并编辑.
输入../回到你的hexo本地目录. hexo g -d重新生成部署

### 在站点配置文件加入
```ini
server:
  port: 5000
  compress: true
  header: true
```