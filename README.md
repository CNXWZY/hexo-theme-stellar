# Fix: Duplicate ID and HTML Attribute Escaping Issues

## 问题描述

### 1. 重复的 id="post-meta" (违反 HTML 规范)
主题模板中使用了硬编码的 `id="post-meta"`，在文章列表页中同一页面会出现多次，违反 HTML 规范（id 必须唯一）。

### 2. HTML 属性中 `&quot;` 转义问题 (导致 JS 语法错误)
在 `onclick`、`onerror` 等 HTML 属性值中，内部使用 `&quot;` 转义双引号。浏览器解析后 `&quot;` 变成 `"`，导致 JS 语法错误，VSCode 报错。

**示例：**
```html
<!-- 模板代码 -->
onclick="document.getElementById(&quot;search-input&quot;).focus();"

<!-- 浏览器解析后（无效 JS） -->
onclick="document.getElementById("search-input").focus();">
                              ^^^^^^^^ 引号提前结束
```

---

## 修复内容

### 1. 修复重复 id 问题 → 改用 class

| 文件 | 修改 |
|------|------|
| `layout/_partial/main/post_list/post_card.ejs` | `id="post-meta"` → `class="post-meta"` |
| `layout/_partial/main/navbar/dateinfo.ejs` | `id="post-meta"` → `class="post-meta"` |
| `layout/_partial/main/notebook/note_card.ejs` | `id="post-meta"` → `class="post-meta"` |
| `source/css/_components/partial/bread-nav.styl` | `div#post-meta` → `.post-meta` |
| `source/js/main.js` | `#post-meta` → `.post-meta` |

### 2. 修复 HTML 属性转义问题 → 使用单引号

| 文件 | 修改位置 |
|------|----------|
| `layout/_partial/sidebar/search.ejs` | onclick |
| `layout/_partial/main/article/article_footer.ejs` | onclick |
| `scripts/tags/lib/posters.js` | onerror |
| `scripts/tags/lib/sites.js` | onerror, style |
| `scripts/tags/lib/image.js` | onerror |
| `scripts/tags/lib/friends.js` | onerror, style |
| `scripts/tags/lib/copy.js` | onclick |
| `scripts/tags/lib/albums.js` | onerror |

---

## 影响

- ✅ 解决 VSCode 等编辑器的 JS 语法报错
- ✅ 生成的 HTML 符合 HTML 规范
- ✅ 浏览器能正确执行 JS 代码

---

## 验证

修复后执行 `hexo clean && hexo generate`，VSCode 不再报告 JS 语法错误。