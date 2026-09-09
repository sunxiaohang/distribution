# distribution — 软件分发仓库（公开）

本仓库用于存放各软件的版本信息（`latest.json`）。**安装包 exe 不进 git，通过 GitHub Release 手动上传附件。**

## 目录结构

```
distribution/
├── excelreader/          # 软件 1：ExcelReader
│   └── latest.json       # 版本信息（软件端更新检查读这个）
└── tool-b/               # 软件 2（未来新增，各占一个目录）
    └── latest.json
```

仓库里**只放 `latest.json`**，不放安装包。

## latest.json 格式

```json
{
  "version": "0.1.003",
  "url": "https://github.com/sunxiaohang/distribution/releases/download/excelreader-v0.1.003/ExcelReader-Setup-0.1.003.exe",
  "changelog": "更新说明"
}
```

| 字段 | 含义 |
|------|------|
| `version` | 最新版本号（软件端用它和自己版本比对） |
| `url` | 安装包下载地址（指向 GitHub Release 附件） |
| `changelog` | 更新说明 |

> 下载地址格式：`https://github.com/<owner>/<repo>/releases/download/<tag>/<文件名>`

## 软件端如何对接

软件源码仓库的 `version.json` 里配置 `updateUrl` 指向本仓库对应软件的 `latest.json`：

```json
"updateUrl": "https://raw.githubusercontent.com/sunxiaohang/distribution/main/excelreader/latest.json"
```

## 发版流程

1. 软件源码里改 `version.json` 的 `version` → 新版本号
2. 打包生成 `ExcelReader-Setup-0.1.003.exe`
3. **GitHub 上创建 Release**：
   - Tag 填 `excelreader-v0.1.003`（带软件名前缀，多软件不冲突）
   - 手动上传 `ExcelReader-Setup-0.1.003.exe` 作为附件
   - 发布
4. 更新 `excelreader/latest.json`（version + url）
5. 提交并 push：

```bash
git add .
git commit -m "excelreader v0.1.003"
git push
```

## 多软件说明

- 每个软件一个子目录放 `latest.json`
- Release 的 tag 用软件名前缀区分：`excelreader-v0.1.003`、`toolb-v1.0.0`
- 各软件的 `latest.json` 里 url 指向各自 Release 的附件

## 注意

- **仓库必须公开**：`latest.json` 走 raw 直链、exe 走 Release 附件下载，都需要公开仓库，否则无法匿名访问。
- **分支名**：默认 `main`，如果实际分支不同，软件端 `updateUrl` 里的 `main` 要改。
