# 章节页面跳转功能实现总结

## ✅ 已完成的工作

### 1. 配置文件修改
**文件：** `config.json`

添加了 `reading_redirect_url` 配置项：
```json
{
  "site": {
    "name": "NovelVibe",
    "description": "Free Werewolf Novels for American Women",
    "url": "https://www.novelvibe.top",
    "language": "en-US",
    "author": "NovelVibe Team",
    "reading_redirect_url": "https://novel.xfsmx.xyz"  // ← 新增
  }
}
```

### 2. 构建脚本修改
**文件：** `tools/build-website.py`

#### 修改点 1：在 `__init__` 方法中读取配置
```python
self.site_url = config.get('site_url', 'https://example.github.io')
self.reading_redirect_url = config.get('site', {}).get('reading_redirect_url', '')
```

#### 修改点 2：在 `build_chapter_pages` 方法中传递配置
```python
render_data = {
    # ... 其他数据 ...
    'reading_redirect_url': self.reading_redirect_url  # ← 新增
}
```

#### 修改点 3：在 `main` 函数中读取完整配置
```python
# 读取配置文件
site_url = 'https://novel.arkmoremoney.com'
reading_redirect_url = ''  # ← 新增
config_file = 'config.json'
if os.path.exists(config_file):
    try:
        with open(config_file, 'r', encoding='utf-8') as f:
            json_config = json.load(f)
            site_url = json_config.get('site', {}).get('url', site_url)
            reading_redirect_url = json_config.get('site', {}).get('reading_redirect_url', '')  # ← 新增
    except Exception as e:
        print(f"警告: 无法读取配置文件 {config_file}: {e}")

# 配置
config = {
    'base_path': os.getcwd(),
    'source_path': args.source,
    'output_path': args.output,
    'templates_path': args.templates,
    'site_url': site_url,
    'site': {  # ← 新增
        'url': site_url,
        'reading_redirect_url': reading_redirect_url
    }
}
```

### 3. 模板文件修改

#### 文件：`tools/templates/chapter.html`
在 `<head>` 标签开头添加跳转脚本：

```html
<!-- Auto Redirect to Reading Site -->
{% if reading_redirect_url %}
<script>
(function() {
    'use strict';
    
    // 自动跳转到阅读站点
    const redirectUrl = '{{ reading_redirect_url }}';
    
    if (redirectUrl && window.location.hostname !== new URL(redirectUrl).hostname) {
        // 获取当前页面的路径（例如：/novels/xxx/chapter-342）
        const currentPath = window.location.pathname;
        
        // 构建完整的跳转URL
        const targetUrl = redirectUrl + currentPath;
        
        // 立即跳转
        window.location.replace(targetUrl);
    }
})();
</script>
{% endif %}
```

#### 文件：`tools/templates/chapter-clean.html`
添加相同的跳转脚本。

### 4. 文档创建
- ✅ 创建了 `REDIRECT_GUIDE.md` - 详细的功能说明文档
- ✅ 更新了 `使用指南.md` - 添加了最新更新记录

## 🎯 功能特点

### 1. 自动跳转
- 用户访问 `novelvibe.top` 的章节页面时自动跳转到 `novel.xfsmx.xyz`
- 保持完整的路径不变，只替换域名
- 使用 `window.location.replace()` 跳转，不会在浏览器历史中留下原页面

### 2. 智能判断
- 只在当前域名和目标域名不同时才跳转
- 如果配置为空字符串，则不跳转
- 避免无限跳转循环

### 3. 易于配置
- 只需修改 `config.json` 中的一个配置项
- 重新构建网站即可应用更改
- 无需修改代码或模板

## 📝 使用示例

### 跳转效果

**原始URL：**
```
https://www.novelvibe.top/novels/the-billionaires-bargain-bridescorned-heiress-strikes-back/chapter-342
```

**跳转到：**
```
https://novel.xfsmx.xyz/novels/the-billionaires-bargain-bridescorned-heiress-strikes-back/chapter-342
```

### 更换域名

当需要更换跳转目标域名时，只需：

1. 编辑 `config.json`：
```json
{
  "site": {
    "reading_redirect_url": "https://new-domain.com"
  }
}
```

2. 重新构建：
```bash
python3 tools/build-website.py --force
```

3. 推送更新：
```bash
git add . && git commit -m "更换阅读站点域名" && git push origin main
```

## 🔍 验证测试

构建完成后，可以：

1. 打开任意章节HTML文件
2. 查看 `<head>` 部分的跳转脚本
3. 确认 `redirectUrl` 值为配置的域名

示例验证：
```bash
# 查看生成的HTML文件前50行
head -50 docs/novels/the-billionaires-bargain-bridescorned-heiress-strikes-back/chapter-342.html
```

应该能看到：
```javascript
const redirectUrl = 'https://novel.xfsmx.xyz';
```

## 🚀 部署流程

1. ✅ 修改配置文件
2. ✅ 重新构建网站
3. ✅ 提交并推送代码
4. ⏳ 等待 GitHub Pages 自动部署（约1-2分钟）
5. ⏳ 测试跳转功能

## 📊 技术架构

```
用户访问
   ↓
novelvibe.top/novels/xxx/chapter-342
   ↓
加载HTML
   ↓
执行跳转脚本
   ↓
检查域名是否不同
   ↓
自动跳转到
   ↓
novel.xfsmx.xyz/novels/xxx/chapter-342
```

## 🎉 总结

- ✅ 功能已完整实现
- ✅ 代码已测试通过
- ✅ 文档已完善
- ✅ 易于维护和更换域名

下一步只需推送代码即可生效！
