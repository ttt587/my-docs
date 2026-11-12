# 开源软件学习笔记

欢迎来到我的个人技术文档网站。本网站记录我在开源软件课程中学习 Markdown、MkDocs、GitHub Actions 和 GitHub Pages 的过程。

- 作者：`ttt587`
- 实践主题：静态文档的自动构建与部署
- 项目源码：[GitHub 仓库](https://github.com/ttt587/my-docs)

## 网站内容

- [关于项目](about.md)：介绍实践目标和各项工具的作用。
- [使用指南](guide.md)：说明如何修改、预览和发布文档。
- [API 参考](api.md)：用一个示例接口演示技术文档的写法。

## 我的实践体会

这次实践让我理解了“源码”和“网站”之间的区别：我维护的是 `docs/` 目录中的 Markdown 源文件，MkDocs 将它们转换为 `site/` 目录中的静态网页，GitHub Actions 再把网页发布到 GitHub Pages。以后只需修改并推送文档，网站就能自动更新。

