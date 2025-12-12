# 章节页面自动跳转功能说明

## 📋 功能概述

当用户访问 `novelvibe.top` 的章节页面时，会自动跳转到 `novel.xfsmx.xyz` 对应的章节页面进行阅读。

## 🎯 工作原理

1. 用户访问：`https://www.novelvibe.top/novels/the-billionaires-bargain-bridescorned-heiress-strikes-back/chapter-342`
2. 页面加载后立即执行跳转脚本
3. 自动跳转到：`https://novel.xfsmx.xyz/novels/the-billionaires-bargain-bridescorned-heiress-strikes-back/chapter-342`

## 🔧 配置方法

### 修改跳转目标域名

在 `config.json` 文件中修改 `reading_redirect_url` 配置：

```json
{
  "site": {
    "name": "NovelVibe",
    "description": "Free Werewolf Novels for American Women",
    "url": "https://www.novelvibe.top",
    "language": "en-US",
    "author": "NovelVibe Team",
    "reading_redirect_url": "https://novel.xfsmx.xyz"  // 👈 修改这里
  }
}
```

### 禁用跳转功能

如果想禁用跳转功能，只需将 `reading_redirect_url` 设置为空字符串：

```json
{
  "site": {
    ...
    "reading_redirect_url": ""  // 设置为空即可禁用跳转
  }
}
```

## 🚀 应用更改

修改配置后，重新构建网站：

```bash
python3 tools/build-website.py --force
```

## 📝 技术细节

### 跳转逻辑

跳转脚本会：
1. 检查配置的跳转URL是否存在
2. 比较当前域名和目标域名是否不同
3. 如果不同，则保持路径不变，只替换域名
4. 使用 `window.location.replace()` 实现跳转（不会在浏览器历史记录中留下原页面）

### 应用范围

跳转功能应用于：
- ✅ `chapter.html` - 带广告的章节页面
- ✅ `chapter-clean.html` - 无广告的章节页面

### 不影响的页面

跳转功能**不会**影响：
- ❌ 首页 (index.html)
- ❌ 小说详情页 (index.html in novel folder)
- ❌ 其他页面

## 🔍 验证方法

1. 构建网站后，打开任意章节HTML文件
2. 在 `<head>` 标签中查找 "Auto Redirect to Reading Site" 注释
3. 检查跳转脚本中的 `redirectUrl` 是否为配置的域名

示例：
```html
<!-- Auto Redirect to Reading Site -->
<script>
(function() {
    'use strict';
    
    // 自动跳转到阅读站点
    const redirectUrl = 'https://novel.xfsmx.xyz';
    
    if (redirectUrl && window.location.hostname !== new URL(redirectUrl).hostname) {
        const currentPath = window.location.pathname;
        const targetUrl = redirectUrl + currentPath;
        window.location.replace(targetUrl);
    }
})();
</script>
```

## ⚙️ 相关文件

- `config.json` - 配置文件
- `tools/build-website.py` - 构建脚本（读取配置并传递给模板）
- `tools/templates/chapter.html` - 章节页面模板（带广告版本）
- `tools/templates/chapter-clean.html` - 章节页面模板（无广告版本）

## 📅 更新历史

- **2025年12月12日** - 添加章节页面自动跳转功能
  - 在配置文件中添加 `reading_redirect_url` 配置项
  - 修改构建脚本以读取和传递跳转URL
  - 在章节模板中添加自动跳转逻辑
