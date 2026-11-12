# API 参考

本页用于练习 API 文档的写法，下面的接口仅用于展示，不提供真实服务。

## 查询文档列表

- 请求方法：`GET`
- 请求路径：`/api/docs`
- 功能：返回当前网站中的文档条目列表。

### 请求示例

```http
GET /api/docs HTTP/1.1
Host: example.com
Accept: application/json
```

### 成功响应

状态码：`200 OK`

```json
{
  "items": [
    {"id": 1, "title": "关于项目", "path": "/about/"},
    {"id": 2, "title": "使用指南", "path": "/guide/"}
  ],
  "total": 2
}
```

### 字段说明

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `items` | array | 文档条目数组 |
| `id` | integer | 文档编号 |
| `title` | string | 文档标题 |
| `path` | string | 文档页面路径 |
| `total` | integer | 返回的文档总数 |

返回[首页](index.md)或查看[使用指南](guide.md)。

