# 🔄 快速参考：更换阅读站点域名

## 当前配置
- 源站点：`https://www.novelvibe.top`
- 跳转目标：`https://novel.xfsmx.xyz`

## 更换域名步骤

### 1️⃣ 编辑配置文件
打开 `config.json`，找到并修改：

```json
{
  "site": {
    "reading_redirect_url": "https://新域名.com"  // 👈 修改这里
  }
}
```

### 2️⃣ 重新构建网站
```bash
python3 tools/build-website.py --force
```

### 3️⃣ 提交并推送
```bash
git add . && git commit -m "更换阅读站点域名到新域名" && git push origin main
```

## 临时禁用跳转
如果需要临时禁用跳转功能，设置为空：

```json
{
  "site": {
    "reading_redirect_url": ""  // 空字符串 = 禁用跳转
  }
}
```

## 常见问题

### Q: 跳转会影响SEO吗？
A: 不会，搜索引擎爬虫会被识别，不会触发跳转。

### Q: 如果在跳转目标站点访问会怎样？
A: 脚本会检测域名，如果已经在目标站点，不会再跳转。

### Q: 需要修改代码吗？
A: 不需要，只需修改 `config.json` 配置文件。

### Q: 多久生效？
A: 推送后等待 GitHub Pages 部署（约1-2分钟）。

## 相关文档
- 详细说明：[REDIRECT_GUIDE.md](REDIRECT_GUIDE.md)
- 实现总结：[REDIRECT_IMPLEMENTATION_SUMMARY.md](REDIRECT_IMPLEMENTATION_SUMMARY.md)
- 使用指南：[使用指南.md](使用指南.md)
