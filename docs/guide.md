# 使用指南

## 修改与发布流程

1. 修改 `docs/` 目录中的 Markdown 文件。
2. 在本地执行严格构建，检查链接和配置。
3. 启动本地服务，逐页检查导航、表格和代码块。
4. 提交修改并推送到 `main` 分支。
5. 在 GitHub Actions 页面查看 `build` 和 `deploy` 状态。
6. 打开 GitHub Pages 网站，确认内容已经更新。

## 常用命令

```powershell
# 严格构建，出现警告时返回失败
.\.venv\Scripts\python.exe -m mkdocs build --strict

# 启动本地预览
.\.venv\Scripts\python.exe -m mkdocs serve

# 查看改动并提交
git status
git add docs
git commit -m "更新文档内容"
git push origin main
```

## 自动部署何时运行

工作流监听 `main` 分支中的 `docs/**`、`mkdocs.yml`、`requirements.txt` 和部署文件。修改这些文件并推送时会自动运行；只修改根目录 README 或实验报告不会触发网站部署。

!!! tip "排错方法"
    如果部署失败，先打开 Actions 运行记录，找到第一个失败的步骤，再阅读其日志。严格构建失败时，应优先检查文件路径、页面链接以及 YAML 是否使用了正确的空格缩进。

继续阅读：[API 参考](api.md)。

