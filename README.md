# 开源软件学习笔记

这是开源软件课程实践项目 2 的个人仓库。项目使用 Markdown 编写内容，使用 MkDocs Material 构建静态网站，并通过 GitHub Actions 自动部署到 GitHub Pages。

- [在线网站](https://ttt587.github.io/my-docs/)
- [个人实验报告](report/README.md)

## 本地预览

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m mkdocs serve
```

浏览器打开终端显示的地址（通常是 <http://127.0.0.1:8000/>）即可预览。

