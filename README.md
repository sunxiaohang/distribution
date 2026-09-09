# distribution — 软件分发仓库（公开）

本仓库用于分发各软件的版本信息（`latest.json`）与安装包 exe。**exe 直接存放在对应软件的文件夹里，随 git 一起提交。**

## 目录结构

```
distribution/
├── excelreader/          # 软件 1：ExcelReader
│   ├── latest.json       # 版本信息（软件端更新检查读这个）
│   └── ExcelReader-Setup-x.x.x.exe   # 安装包（提交到 git）
└── tool-b/               # 软件 2（未来新增，各占一个目录）
    ├── latest.json
    └── ToolB-Setup-x.x.x.exe
```

每个软件一个子目录，`latest.json` 和 exe 都放在自己目录里，互不干扰。

## latest.json 格式

```json
{
  "version": "0.1.003",
  "url": "https://raw.githubusercontent.com/sunxiaohang/distribution/main/excelreader/ExcelReader-Setup-0.1.003.exe",
  "changelog": "更新说明"
}
```

| 字段 | 含义 |
|------|------|
| `version` | 最新版本号（软件端用它和自己版本比对） |
| `url` | 安装包直链（raw 地址，用户点「查看更新」时下载） |
| `changelog` | 更新说明 |

> raw 直链格式：`https://raw.githubusercontent.com/<owner>/<repo>/<branch>/<软件目录>/<文件名>`

## 软件端如何对接

软件源码仓库的 `version.json` 里配置 `updateUrl` 指向本仓库对应软件的 `latest.json`：

```json
"updateUrl": "https://raw.githubusercontent.com/sunxiaohang/distribution/main/excelreader/latest.json"
```

## 发版流程

1. 软件源码里改 `version.json` 的 `version` → 新版本号
2. 打包生成 `ExcelReader-Setup-0.1.003.exe`
3. 复制 exe 到本仓库的 `excelreader/` 目录
4. 更新 `excelreader/latest.json`（version + url）
5. 提交并 push：

```bash
git add .
git commit -m "excelreader v0.1.003"
git push
```

## 注意

- **仓库必须公开**：更新检查走 raw 直链，私有仓库无法匿名访问。
- **分支名**：默认 `main`，如果实际分支不同，`latest.json` 和软件端 `updateUrl` 里的 `main` 都要改。
- **单文件 < 100MB**：GitHub 单文件上限 100MB。安装包通常几 MB 没问题，但仓库体积会随历史版本累积（每版一个 exe），可定期清理旧版本 exe。
