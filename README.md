# FairyGUI Editor 下载镜像

自动镜像 [FairyGUI Editor](https://fairygui.com/release/editor) 官方安装包到 GitHub Releases，提供**稳定直链**、**历史版本归档**和**自动化友好的查询接口**。

由 GitHub Actions 每天定时检查官网，发现新版本就自动下载、建 Release、更新查询端点。

> 本仓库仅做自动化镜像与归档，所有安装包版权归 [FairyGUI](https://fairygui.com) 官方所有。

---

## 查询最新版本

**方式 1 — jsDelivr CDN（推荐，国内可达、无 API 限流）**

```bash
curl -s https://cdn.jsdelivr.net/gh/hwei/fairygui-editor-mirror@main/latest.json
```

返回：

```json
{
  "version": "6.1.4",
  "published_at": "2026-06-01T02:00:00Z",
  "windows": "https://github.com/hwei/fairygui-editor-mirror/releases/download/v6.1.4/FairyGUI-Editor_6.1.4.zip",
  "macos":   "https://github.com/hwei/fairygui-editor-mirror/releases/download/v6.1.4/FairyGUI-Editor_Mac_6.1.4.zip",
  "release": "https://github.com/hwei/fairygui-editor-mirror/releases/tag/v6.1.4"
}
```

> ⚠️ jsDelivr 对分支文件有缓存（约 12 小时）。每次发版时 Action 会自动调用 `purge.jsdelivr.net` 刷新，通常很快生效。

**方式 2 — GitHub Release API（匿名 60 次/小时限流）**

```bash
curl -s https://api.github.com/repos/hwei/fairygui-editor-mirror/releases/latest | jq -r .tag_name
```

**方式 3 — 网页**

直接打开 <https://github.com/hwei/fairygui-editor-mirror/releases/latest>。

---

## 下载

**只取版本号：**

```bash
curl -s https://cdn.jsdelivr.net/gh/hwei/fairygui-editor-mirror@main/latest.json | jq -r .version
```

**下载最新版（Windows）：**

```bash
url=$(curl -s https://cdn.jsdelivr.net/gh/hwei/fairygui-editor-mirror@main/latest.json | jq -r .windows)
curl -fL -o FairyGUI-Editor.zip "$url"
```

**下载最新版（macOS）：**

```bash
url=$(curl -s https://cdn.jsdelivr.net/gh/hwei/fairygui-editor-mirror@main/latest.json | jq -r .macos)
curl -fL -o FairyGUI-Editor-Mac.zip "$url"
```

**下载指定历史版本：**

```
https://github.com/hwei/fairygui-editor-mirror/releases/download/v6.1.4/FairyGUI-Editor_6.1.4.zip
https://github.com/hwei/fairygui-editor-mirror/releases/download/v6.1.4/FairyGUI-Editor_Mac_6.1.4.zip
```

GitHub Release 资产走 CDN，**无 Referer 限制**，可直接 `curl`/`wget`/浏览器下载。

---

## 工作原理

官网是 Vue SPA，版本号被打包进 JS 分包里（`JSON.parse('{"version":"x.y.z"}')`），下载链接按
`https://res.fairygui.com/FairyGUI-Editor_<版本>.zip` 规则拼出。Action 解析出版本号后，
带 `Referer: https://fairygui.com/` 从原站下载（原站直链有 Referer ACL，否则 403），
校验为合法 zip 后发布到 Release 并更新 `latest.json`。

详见 [`.github/workflows/mirror.yml`](.github/workflows/mirror.yml)。

## 维护

- **手动触发 / 强制重镜像**：Actions → *Mirror FairyGUI Editor* → *Run workflow*（可勾选 `force`）。
- **解析失败告警**：官网若改版导致解析不到版本号，Action 会直接报错失败，可在 Actions 页面看到。
