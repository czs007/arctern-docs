# 轮廓图

根据 SQL 语句以及相关画图参数绘制轮廓图，将绘图结果以 base64 编码方式返回。

## 请求说明

- HTTP方法: **POST**
- 请求URL: `/choroplethmap`
- Headers:
    - `Content-Type: application/json`
- Body:
```json
{
    "scope": "scope_name",
    "session": "session_name",
    "sql": "select ST_GeomFromText(col_wkb_polygon) as polygon, col2 as count from table_name",
    "params": {
        "width": 1024,
        "height": 896,
        "bounding_box": [-75.37976, 40.191296, -71.714099, 41.897445],
        "coordinate_system": "EPSG:4326",
        "color_gradient": ["#0000FF", "#FF0000"],
        "color_bound": [2.5, 5],
        "opacity": 1,
        "aggregation_type": "sum"
    }
}
```

参数说明：

- scope：执行绘制轮廓图操作的作用域名称；
- session：可选参数，执行绘制轮廓图操作的 `SparkSession` 名称；
- sql：待执行的 SQL 查询语句，该查询的结果作为绘制轮廓图的渲染对象；
- params：绘图参数，具体说明如下，详见 [Arctern-Spark 绘图接口文档](../../../spark/api/render/function/layer/choroplethmap.md)：
    - width：图片宽度；
    - height：图片高度；
    - bounding_box：渲染图片所表示的地理范围 [`x_min`, `y_min`, `x_max`, `y_max`]；
    - coordinate_system：输入数据的坐标系统，详见 [World Geodetic System](https://en.wikipedia.org/wiki/World_Geodetic_System)；
    - color_gradient：轮廓的颜色渐变范围，即轮廓的颜色从左边渐变到右边；
    - color_bound：轮廓颜色的取值范围，与 `color_gradient` 配合使用；
    - opacity：轮廓的不透明度。
    - aggregation_type：聚合类型。

## 样例

### python

本文示例代码使用 python 的 `requests` 库调用 `Arctern Restful API`，使用下面的命令安装 `requests`：

```shell
pip install requests
```

调用示例：

```python
import requests
import json

url = "http://localhost:8080/choroplethmap"

payload  = {
    "scope": "scope_name",
    "session": "session_name",
    "sql": "select ST_GeomFromText(col_wkb_polygon) as polygon, col2 as count from table_name",
    "params": {
        "width": 1024,
        "height": 896,
        "bounding_box": [-75.37976, 40.191296, -71.714099, 41.897445],
        "coordinate_system": "EPSG:4326",
        "color_gradient": ["#0000FF", "#FF0000"],
        "color_bound": [2.5, 5],
        "opacity": 1
    }
}
headers = {
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=json.dumps(payload))

print(response.text.encode('utf8'))
```

### curl

```shell
curl --location --request POST 'http://localhost:8080/choroplethmap' \
--header 'Content-Type: application/json' \
--data-raw '{
    "scope": "scope_name",
    "session": "session_name",
    "sql": "select ST_GeomFromText(col_wkb_polygon) as polygon, col2 as count from table_name",
    "params": {
        "width": 1024,
        "height": 896,
        "bounding_box": [-75.37976, 40.191296, -71.714099, 41.897445],
        "coordinate_system": "EPSG:4326",
        "color_gradient": ["#0000FF", "#FF0000"],
        "color_bound": [2.5, 5],
        "opacity": 1
    }
}'
```

## 返回说明

成功样例：

```json
{
    "status": "success",
    "code": "200",
    "result": "使用 base64 编码后的轮廓图数据"
}
```

失败样例：

```json
{
    "code": -1,
    "message": "Table or view not found: error_table; line 1 pos 14;\n'Project [*]\n+- 'UnresolvedRelation [error_table]\n",
    "status": "error"
}
```

