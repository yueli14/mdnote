

## windows安装

```properties
简单配置
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
network.host: 127.0.0.1
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
http.port: 9200

#跨域配置

http.cors.enabled: true

http.cors.allow-origin: "*"

#修改安全
# Enable security features
xpack.security.enabled: false

xpack.security.enrollment.enabled: false

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
# Create a new cluster with the current node only
# Additional nodes can still join the cluster later
cluster.initial_master_nodes: ["DESKTOP-SO0MQ39"]
```

## 增删改查

### 索引操作

#### 创建索引

```
向 ES 服务器发 PUT 请求 ：http://127.0.0.1:9200/shopping
```

#### 查看所有索引

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/_cat/indices?v
```

#### 查看单个索引

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/shopping
```

#### 删除索引

```
向 ES 服务器发 DELETE 请求 ：http://127.0.0.1:9200/shopping
```

### 文档操作

#### 创建文档

```
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_doc
请求内容json
{
 "title":"小米手机",
 "category":"小米",
 "images":"http://www.gulixueyuan.com/xm.jpg",
 "price":3999.00
}
要自定义唯一性标识，需要在创建时指定：http://127.0.0.1:9200/shopping/_doc/1
```

#### 查看文档

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/shopping/_doc/1
```

#### 修改文档

```
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_doc/1
{
 "title":"华为手机",
 "category":"华为",
 "images":"http://www.gulixueyuan.com/hw.jpg",
 "price":4999.00
}
```

#### 修改字段

```
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_update/1
{ 
 "doc": {
 "price":3000.00
 } 
}
```

#### 删除文档

```
向 ES 服务器发 DELETE 请求 ：http://127.0.0.1:9200/shopping/_doc/1
```

#### 条件删除文档

```
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_delete_by_query
{
 "query":{
 "match":{
 "price":4000.00
 }
 }
}
```

### 映射操作

#### 创建映射

```
向 ES 服务器发 PUT 请求 ：http://127.0.0.1:9200/student/_mapping
body
{
 "properties": {
 "name":{
 "type": "text",
 "index": true
 },
 "sex":{
 "type": "text",
 "index": false
 },
 "age":{
 "type": "long",
 "index": false
 }
 }
}

映射数据说明：
 字段名：任意填写，下面指定许多属性，例如：title、subtitle、images、price
 type：类型，Elasticsearch 中支持的数据类型非常丰富，说几个关键的：
 String 类型，又分两种：
    text：可分词
    keyword：不可分词，数据会作为完整字段进行匹配
 Numerical：数值类型，分两类
    基本数据类型：long、integer、short、byte、double、float、half_float
    浮点数的高精度类型：scaled_float
 Date：日期类型
 Array：数组类型
 Object：对象
 index：是否索引，默认为 true，也就是说你不进行任何配置，所有字段都会被索引。
    true：字段会被索引，则可以用来进行搜索
    false：字段不会被索引，不能用来搜索
 store：是否将数据进行独立存储，默认为 false
    原始的文本会存储在_source 里面，默认情况下其他提取出来的字段都不是独立存储的，是从_source 里面提取出来的。当然你也可以独立的存储某个字段，只要设置"store": true 即可，获取独立存储的字段要比从_source 中解析快得多，但是也会占用更多的空间，所以要根据实际业务需求来设置。
 analyzer：分词器，这里的 ik_max_word 即使用 ik 分词器
```

#### 查看映射

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_mapping
```

#### 索引映射关联

```
向 ES 服务器发 PUT 请求 ：http://127.0.0.1:9200/student1
{
 "settings": {},
 "mappings": {
 "properties": {
"name":{
 "type": "text",
 "index": true
 
},
"sex":{
 "type": "text",
 "index": false
},
"age":{
 "type": "long",
 "index": false
}
 }
 }
}
```

### 高级查询

#### 数据

```
# POST /student/_doc/1001
{
"name":"zhangsan",
"nickname":"zhangsan",
 "sex":"男",
 "age":30
}
# POST /student/_doc/1002
{
"name":"lisi",
"nickname":"lisi",
 "sex":"男",
 "age":20
}
# POST /student/_doc/1003
{
"name":"wangwu",
 "nickname":"wangwu",
 "sex":"女",
 "age":40
}
# POST /student/_doc/1004
{
"name":"zhangsan1",
"nickname":"zhangsan1",
 "sex":"女",
 "age":50
}
# POST /student/_doc/1005
{
"name":"zhangsan2",
"nickname":"zhangsan2",
 "sex":"女",
 "age:90}
```

#### 查询所有文档

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match_all": {}
 }
}
# "query"：这里的 query 代表一个查询对象，里面可以有不同的查询属性
# "match_all"：查询类型，例如：match_all(代表查询所有)， match，term ， range 等等
# {查询条件}：查询条件会根据类型的不同，写法也有差异
```

#### 匹配查询

```
match 匹配类型查询，会把查询条件进行分词，然后进行查询，多个词条之间是 or 的关系
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match": {
"name":"zhangsan"
 }
 }
}
```

#### 字段匹配查询

```
multi_match 与 match 类似，不同的是它可以在多个字段中查询。
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "multi_match": {
 "query": "zhangsan",
 "fields": ["name","nickname"]
 }
 }
}
```

#### 关键字精确查询

```
term 查询，精确的关键词匹配查询，不对查询条件进行分词。
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "term": {
 "name": {
 "value": "zhangsan"
 }
 }
 }
}
```

#### 多关键字精确查询

```
terms 查询和 term 查询一样，但它允许你指定多值进行匹配。
如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件，类似于 mysql 的 in
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "terms": {
 "name": ["zhangsan","lisi"]
 }
 }
}

```

#### 指定查询字段

```
默认情况下，Elasticsearch 在搜索的结果中，会把文档中保存在_source 的所有字段都返回。
如果我们只想获取其中的部分字段，我们可以添加_source 的过滤
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "_source": ["name","nickname"], 
 "query": {
 "terms": {
 "nickname": ["zhangsan"]
 }
 }
}

```

#### 过滤字段

```
 includes：来指定想要显示的字段
 excludes：来指定不想要显示的字段
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "_source": {
 "includes": ["name","nickname"]
 }, 
 "query": {
 "terms": {
 "nickname": ["zhangsan"]
 }
 }
}

{
 "_source": {
 "excludes": ["name","nickname"]
 }, 
 "query": {
 "terms": {
 "nickname": ["zhangsan"]
 }
 }
}
```

#### 组合查询

```
`bool`把各种其它查询通过`must`（必须 ）、`must_not`（必须不）、`should`（应该）的方式进行组合
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "bool": {
 "must": [
 {
 "match": {
 "name": "zhangsan"
 }
 }
 ],
 "must_not": [
 {
 "match": {
 "age": "40"
 }
 }
 ],
 "should": [
 {
 "match": {
 "sex": "男"
 }
 }
 ]
 }
 }
}
```

#### 范围查询

```
gt 大于>
gte 大于等于>=
lt 小于<
lte 小于等于<=
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "range": {
 "age": {
 "gte": 30,
 "lte": 35
 }
 }
 }
}
```

####  模糊查询

```
返回包含与搜索字词相似的字词的文档。
编辑距离是将一个术语转换为另一个术语所需的一个字符更改的次数。这些更改可以包括：
 更改字符（box → fox）
 删除字符（black → lack）
—————————————————————————————
更多 Java –大数据 –前端 –python 人工智能资料下载，可百度访问：尚硅谷官网
 插入字符（sic → sick）
 转置两个相邻字符（act → cat）
为了找到相似的术语，fuzzy 查询会在指定的编辑距离内创建一组搜索词的所有可能的变体或扩展。然后查询返回每个扩展的完全匹配。
通过 fuzziness 修改编辑距离。一般使用默认值 AUTO，根据术语的长度生成编辑距离。
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "fuzzy": {
 "title": {
 "value": "zhangsan"
 }
 }
 }
}

{
 "query": {
 "fuzzy": {
 "title": {
 "value": "zhangsan",
"fuzziness": 2
 }
 }
 }
}
```

#### 单字段排序

```
sort 可以让我们按照不同的字段进行排序，并且通过 order 指定排序的方式。desc 降序，asc升序。
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match": {
 "name":"zhangsan"
 }
 },
 "sort": [{
 "age": {
 "order":"desc"
 }
 }]
}
```

#### 多字段排序

```
假定我们想要结合使用 age 和 _score 进行查询，并且匹配的结果首先按照年龄排序，然后按照相关性得分排序
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match_all": {}
 },
 "sort": [
 {
 "age": {
 "order": "desc"
 }
 },
 {
 "_score":{
 "order": "desc"
 }
 }
 ]
}
```

#### 高亮查询

```
在进行关键字搜索时，搜索出的内容中的关键字会显示不同的颜色，称之为高亮
Elasticsearch 可以对查询内容中的关键字部分，进行标签和样式(高亮)的设置。
在使用 match 查询的同时，加上一个 highlight 属性：
 pre_tags：前置标签
 post_tags：后置标签
 fields：需要高亮的字段
 title：这里声明 title 字段需要高亮，后面可以为这个字段设置特有配置，也可以空
在 Postman 中，向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match": {
 "name": "zhangsan"
 }
 },
 "highlight": {
 "pre_tags": "<font color='red'>",
 "post_tags": "</font>",
 "fields": {
 "name": {}
 }
 }
}
```

#### 分页查询

```
from：当前页的起始索引，默认从 0 开始。 from = (pageNum - 1) * size
size：每页显示多少条
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "query": {
 "match_all": {}
 },
 "sort": [
 {
 "age": {
 "order": "desc"
 }
 }
 ],
 "from": 0,
 "size": 2
}
```

#### 聚合查询

```
聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，当然还有很
多其他的聚合，例如取最大值、平均值等等。
 对某个字段取最大值 max
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
{
 "aggs":{
 "max_age":{
 "max":{"field":"age"}
 }
 },
 "size":0
}
对某个字段取最小值 min
{
 "aggs":{
 "min_age":{
 "min":{"field":"age"}
 }
 },
 "size":0
}
对某个字段求和 sum
{
 "aggs":{
 "sum_age":{
 "sum":{"field":"age"}
 }
 },
 "size":0
}
对某个字段取平均值 avg
{
 "aggs":{
 "avg_age":{
 "avg":{"field":"age"}
 }
 },
 "size":0
}
对某个字段的值进行去重之后再取总数
{
 "aggs":{
 "distinct_age":{
 "cardinality":{"field":"age"}
 }
 },
 "size":0
}
State 聚合
stats 聚合，对某个字段一次性返回 count，max，min，avg 和 sum 五个指标
{
 "aggs":{
 "stats_age":{
 "stats":{"field":"age"}
 }
 },
 "size":0
}
```

#### 桶聚合查询

```
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
桶聚和相当于 sql 中的 group by 语句
 terms 聚合，分组统计
{
 "aggs":{
 "age_groupby":{
 "terms":{"field":"age"}
 }
 },
 "size":0
} 
在 terms 分组下再进行聚合
{
 "aggs":{
 "age_groupby":{
 "terms":{"field":"age"}
 }
 },
 "size":0
} 
```

## javaAPI

#### pom

```xml
    <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>7.8.0</version>
        </dependency>
        <!-- elasticsearch 的客户端 -->
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.8.0</version>
        </dependency>
 <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore</artifactId>
            <version>4.4.13</version>
        </dependency>
```

#### 索引操作

```java

public class EsClient {


    public static void main(String[] args) throws IOException {

        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("127.0.0.1", 9200, "http"))
        );
//        //创建索引
//        //建立请求
//        CreateIndexRequest createIndexRequest = new CreateIndexRequest("user1");
//        //发送请求
//        CreateIndexResponse createIndexResponse = client.indices().create(createIndexRequest, RequestOptions.DEFAULT);
//        //响应结果
//        boolean shardsAcknowledged = createIndexResponse.isShardsAcknowledged();
//        System.out.println("创建成功" + shardsAcknowledged);
        //查询索引

//        GetIndexRequest user = new GetIndexRequest("user");
//        GetIndexResponse getIndexResponse = client.indices().get(user,RequestOptions.DEFAULT);
//        System.out.println(getIndexResponse.getAliases());
//        System.out.println(getIndexResponse.getIndices());

        //删除索引
        DeleteIndexRequest user1 = new DeleteIndexRequest("user1");
        AcknowledgedResponse delete = client.indices().delete(user1, RequestOptions.DEFAULT);
        System.out.println(delete.isAcknowledged());
        client.close();

    }
}
```

#### 文档操作

##### 简单操作

```java
 RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

	 // 插入数据
        IndexRequest request = new IndexRequest();
        request.index("user").id("1001");

        User user = new User();
        user.setName("zhangsan");
        user.setAge(30);
        user.setSex("男");

        // 向ES插入数据，必须将数据转换位JSON格式
        ObjectMapper mapper = new ObjectMapper();
        String userJson = mapper.writeValueAsString(user);
        request.source(userJson, XContentType.JSON);

        IndexResponse response = esClient.index(request, RequestOptions.DEFAULT);

        System.out.println(response.getResult());

        esClient.close();
 // 修改数据
        UpdateRequest request = new UpdateRequest();
        request.index("user").id("1001");
        request.doc(XContentType.JSON, "sex", "女");

        UpdateResponse response = esClient.update(request, RequestOptions.DEFAULT);

        System.out.println(response.getResult());

        esClient.close();
//删除数据
 DeleteRequest request = new DeleteRequest();
        request.index("user").id("1001");

        DeleteResponse response = esClient.delete(request, RequestOptions.DEFAULT);
        System.out.println(response.toString());

//批量删除
 // 批量删除数据
        BulkRequest request = new BulkRequest();

        request.add(new DeleteRequest().index("user").id("1001"));
        request.add(new DeleteRequest().index("user").id("1002"));
        request.add(new DeleteRequest().index("user").id("1003"));

        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(response.getItems());

        esClient.close();
//批量插入
  // 批量插入数据
        BulkRequest request = new BulkRequest();

//        request.add(new IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", "zhangsan", "age",30,"sex","男"));
//        request.add(new IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", "lisi", "age",30,"sex","女"));
//        request.add(new IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", "wangwu", "age",40,"sex","男"));
//        request.add(new IndexRequest().index("user").id("1004").source(XContentType.JSON, "name", "wangwu1", "age",40,"sex","女"));
//        request.add(new IndexRequest().index("user").id("1005").source(XContentType.JSON, "name", "wangwu2", "age",50,"sex","男"));
//        request.add(new IndexRequest().index("user").id("1006").source(XContentType.JSON, "name", "wangwu3", "age",50,"sex","男"));
        //request.add(new IndexRequest().index("user").id("1007").source(XContentType.JSON, "name", "wangwu44", "age",60,"sex","男"));
        //request.add(new IndexRequest().index("user").id("1008").source(XContentType.JSON, "name", "wangwu555", "age",60,"sex","男"));
        request.add(new IndexRequest().index("user").id("1009").source(XContentType.JSON, "name", "wangwu66666", "age",60,"sex","男"));

        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(response.getItems());

        esClient.close();
```

##### 查询操作

```java
RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

            // 1. 查询索引中全部的数据
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        request.source(new SearchSourceBuilder().query(QueryBuilders.matchAllQuery()));
//
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

        // 2. 条件查询 : termQuery
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("age", 30)));
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

        // 3. 分页查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//        // (当前页码-1)*每页显示数据条数
//        builder.from(2);
//        builder.size(2);
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 4. 查询排序
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//        //
//        builder.sort("age", SortOrder.DESC);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 5. 过滤字段
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
//        //
//        String[] excludes = {"age"};
//        String[] includes = {};
//        builder.fetchSource(includes, excludes);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 6. 组合查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder();
//        BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
//
//        //boolQueryBuilder.must(QueryBuilders.matchQuery("age", 30));
//        //boolQueryBuilder.must(QueryBuilders.matchQuery("sex", "男"));
//        //boolQueryBuilder.mustNot(QueryBuilders.matchQuery("sex", "男"));
//        boolQueryBuilder.should(QueryBuilders.matchQuery("age", 30));
//        boolQueryBuilder.should(QueryBuilders.matchQuery("age", 40));
//
//        builder.query(boolQueryBuilder);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 7. 范围查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder();
//        RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");
//
//        rangeQuery.gte(30);
//        rangeQuery.lt(50);
//
//        builder.query(rangeQuery);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

        // 8. 模糊查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder();
//        builder.query(QueryBuilders.fuzzyQuery("name", "wangwu").fuzziness(Fuzziness.TWO));
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 9. 高亮查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder();
//        TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("name", "zhangsan");
//
//        HighlightBuilder highlightBuilder = new HighlightBuilder();
//        highlightBuilder.preTags("<font color='red'>");
//        highlightBuilder.postTags("</font>");
//        highlightBuilder.field("name");
//
//        builder.highlighter(highlightBuilder);
//        builder.query(termsQueryBuilder);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

//        // 10. 聚合查询
//        SearchRequest request = new SearchRequest();
//        request.indices("user");
//
//        SearchSourceBuilder builder = new SearchSourceBuilder();
//
//        AggregationBuilder aggregationBuilder = AggregationBuilders.max("maxAge").field("age");
//        builder.aggregation(aggregationBuilder);
//
//        request.source(builder);
//        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);
//
//        SearchHits hits = response.getHits();
//
//        System.out.println(hits.getTotalHits());
//        System.out.println(response.getTook());
//
//        for ( SearchHit hit : hits ) {
//            System.out.println(hit.getSourceAsString());
//        }

        // 11. 分组查询
        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder();

        AggregationBuilder aggregationBuilder = AggregationBuilders.terms("ageGroup").field("age");
        builder.aggregation(aggregationBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        for ( SearchHit hit : hits ) {
            System.out.println(hit.getSourceAsString());
        }


        esClient.close();     
```

## 集群

#### 单机（参考）

```
useradd es #新增 es 用户
passwd es #为 es 用户设置密码
userdel -r es #如果错了，可以删除再加
chown -R es:es /opt/module/es #文件夹所有者
修改/opt/module/es/config/elasticsearch.yml 文件
# 加入如下配置
cluster.name: elasticsearch
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
修改/etc/security/limits.conf 
# 在文件末尾中增加下面内容
# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536
修改/etc/security/limits.d/20-nproc.conf
# 在文件末尾中增加下面内容
# 每个进程可以打开的文件数的限制
es soft nofile 65536
es hard nofile 65536
 尚硅谷技术之 Elasticsearch 
—————————————————————————————
更多 Java –大数据 –前端 –python 人工智能资料下载，可百度访问：尚硅谷官网
# 操作系统级别对每个用户创建的进程数的限制
* hard nproc 4096
# 注：* 带表 Linux 所有用户名称
修改/etc/sysctl.conf
# 在文件中增加下面内容
# 一个进程可以拥有的 VMA(虚拟内存区域)的数量,默认值为 65536
vm.max_map_count=655360
重新加载
sysctl -p 
3.3.3 启动软件
使用 ES 用户启动
cd /opt/module/es/
#启动
bin/elasticsearch
#后台启动
bin/elasticsearch -d
关闭防火墙
#暂时关闭防火墙
systemctl stop firewalld
#永久关闭防火墙
systemctl enable firewalld.service #打开放货抢永久性生效，重启后不会复原
systemctl disable firewalld.service #关闭防火墙，永久性生效，重启后不会复原
```

#### 集群

```
修改/opt/module/es/config/elasticsearch.yml 文件，分发文件
# 加入如下配置
#集群名称
cluster.name: cluster-es
#节点名称，每个节点的名称不能重复
node.name: node-1
#ip 地址，每个节点的地址不能重复
network.host: linux1
#是不是有资格主节点
node.master: true
node.data: true
http.port: 9200
# head 插件需要这打开这两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true
http.max_content_length: 200mb
#es7.x 之后新增的配置，初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["node-1"]
#es7.x 之后新增的配置，节点发现
discovery.seed_hosts: ["linux1:9300","linux2:9300","linux3:9300"]
gateway.recover_after_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
transport.tcp.compress: true
#集群内同时启动的数据任务个数，默认是 2 个
cluster.routing.allocation.cluster_concurrent_rebalance: 16
#添加或删除节点及负载均衡时并发恢复的线程个数，默认 4 个
cluster.routing.allocation.node_concurrent_recoveries: 16
#初始化数据恢复时，并发恢复线程的个数，默认 4 个
cluster.routing.allocation.node_initial_primaries_recoveries: 16
修改/etc/security/limits.conf ，分发文件
# 在文件末尾中增加下面内容
es soft nofile 65536
es hard nofile 65536
修改/etc/security/limits.d/20-nproc.conf，分发文件
# 在文件末尾中增加下面内容
es soft nofile 65536
es hard nofile 65536
* hard nproc 4096
# 注：* 带表 Linux 所有用户名称
修改/etc/sysctl.conf
# 在文件中增加下面内容
vm.max_map_count=655360
重新加载
sysctl -p
分别在不同节点上启动 ES 软件
cd /opt/module/es-cluster
#启动
bin/elasticsearch
#后台启动
bin/elasticsearch -d
```

## 进阶

### 核心概念

#### 索引（Index）

一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的 索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必 须全部是小写字母），并且当我们要对这个索引中的文档进行索引、搜索、更新和删除的时 候，都要使用到这个名字。在一个集群中，可以定义任意多的索引。 能搜索的数据必须索引，这样的好处是可以提高查询速度，比如：新华字典前面的目录 就是索引的意思，目录可以提高查询速度。 **Elasticsearch 索引的精髓：一切设计都是为了提高搜索的性能。**

#### 类型（Type）

在一个索引中，你可以定义一种或多种类型。 一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具 有一组共同字段的文档定义一个类型。不同的版本，类型发生了不同的变化

版本 Type 5.x 支持多种。 type 6.x 只能有一种。 type 7.x 默认不再支持自定义索引类型（默认类型为：_doc）

#### 文档（Document）

 一个文档是一个可被索引的基础信息单元，也就是一条数据 比如：你可以拥有某一个客户的文档，某一个产品的一个文档，当然，也可以拥有某个 订单的一个文档。文档以 JSON（Javascript Object Notation）格式来表示，而 JSON 是一个 到处存在的互联网数据交互格式。 在一个 index/type 里面，你可以存储任意多的文档。

####  字段（Field）

相当于是数据表的字段，对文档数据根据不同属性进行的分类标识

#### 映射（Mapping）

mapping 是处理数据的方式和规则方面做一些限制，如：某个字段的数据类型、默认值、 分析器、是否被索引等等。这些都是映射里面可以设置的，其它就是处理 ES 里面数据的一 些使用规则设置也叫做映射，按着最优规则处理数据对性能提高很大，因此才需要建立映射， 并且需要思考如何建立映射才能对性能更好。

#### 分片（Shards）

一个索引可以存储超出单个节点硬件限制的大量数据。比如，一个具有 10 亿文档数据 的索引占据 1TB 的磁盘空间，而任一节点都可能没有这样大的磁盘空间。或者单个节点处 理搜索请求，响应太慢。为了解决这个问题，Elasticsearch 提供了将索引划分成多份的能力， 每一份就称之为分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分 片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点 上。

分片很重要，主要有两方面的原因： 

1）允许你水平分割 / 扩展你的内容容量。

 2）允许你在分片之上进行分布式的、并行的操作，进而提高性能/吞吐量。 至于一个分片怎样分布，它的文档怎样聚合和搜索请求，是完全由 Elasticsearch 管理的， 对于作为用户的你来说，这些都是透明的，无需过分关心。

被混淆的概念是，一个 Lucene 索引 我们在 Elasticsearch 称作 分片 。 一个 Elasticsearch 索引 是分片的集合。 当 Elasticsearch 在索引中搜索的时候， 他发送查询 到每一个属于索引的分片(Lucene 索引)，然后合并每个分片的结果到一个全局的结果集。

#### 副本（Replicas）

在一个网络 / 云的环境里，失败随时都可能发生，在某个分片/节点不知怎么的就处于 离线状态，或者由于任何原因消失了，这种情况下，有一个故障转移机制是非常有用并且是 强烈推荐的。为此目的，Elasticsearch 允许你创建分片的一份或多份拷贝，这些拷贝叫做复 制分片(副本)。

复制分片之所以重要，有两个主要原因： 

 在分片/节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与 原/主要（original/primary）分片置于同一节点上是非常重要的。 

 扩展你的搜索量/吞吐量，因为搜索可以在所有的副本上并行运行。

总之，每个索引可以被分成多个分片。一个索引也可以被复制 0 次（意思是没有复制） 或多次。一旦复制了，每个索引就有了主分片（作为复制源的原来的分片）和复制分片（主 分片的拷贝）之别。分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可 以在任何时候动态地改变复制的数量，但是你事后不能改变分片的数量。默认情况下， Elasticsearch 中的每个索引被分片 1 个主分片和 1 个复制，这意味着，如果你的集群中至少 有两个节点，你的索引将会有 1 个主分片和另外 1 个复制分片（1 个完全拷贝），这样的话 每个索引总共就有 2 个分片，我们需要根据索引需要确定分片个数。

#### 分配（Allocation）

将分片分配给某个节点的过程，包括分配主分片或者副本。如果是副本，还包含从主分 片复制数据的过程。这个过程是由 master 节点完成的

### 系统架构

![image-20220218174017822](D:/mdimgs/image-20220218174017822.png)

一个运行中的 Elasticsearch 实例称为一个节点，而集群是由一个或者多个拥有相同 cluster.name 配置的节点组成， 它们共同承担数据和负载的压力。当有节点加入集群中或者 从集群中移除节点时，集群将会重新平均分布所有的数据。 当一个节点被选举成为主节点时， 它将负责管理集群范围内的所有变更，例如增加、 删除索引，或者增加、删除节点等。 而主节点并不需要涉及到文档级别的变更和搜索等操 作，所以当集群只拥有一个主节点的情况下，即使流量的增加它也不会成为瓶颈。 任何节 点都可以成为主节点。我们的示例集群就只有一个节点，所以它同时也成为了主节点。 作为用户，我们可以将请求发送到集群中的任何节点 ，包括主节点。 每个节点都知道 任意文档所处的位置，并且能够将我们的请求直接转发到存储我们所需文档的节点。 无论 我们将请求发送到哪个节点，它都能负责从各个包含我们所需文档的节点收集回数据，并将 最终结果返回給客户端。 Elasticsearch 对这一切的管理都是透明的。

### 分布式集群

####  故障转移

当集群中只有一个节点在运行时，意味着会有一个单点故障问题——没有冗余。 幸运 的是，我们只需再启动一个节点即可防止数据丢失。当你在同一台机器上启动了第二个节点 时，只要它和第一个节点有同样的 cluster.name 配置，它就会自动发现集群并加入到其中。 但是在不同机器上启动节点的时候，为了加入到同一集群，你需要配置一个可连接到的单播 主机列表。之所以配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上 运行的节点才会自动组成集群。

如果启动了第二个节点，我们的集群将会拥有两个节点的集群 : 所有主分片和副本分 片都已被分配

![image-20220218174859448](D:/mdimgs/image-20220218174859448.png)

#### 水平扩容

怎样为我们的正在增长中的应用程序按需扩容呢？当启动了第三个节点，我们的集群将 会拥有三个节点的集群 : 为了分散负载而对分片进行重新分配

![image-20220218174851856](D:/mdimgs/image-20220218174851856.png)

##### 但是如果我们想要扩容超过 6 个节点怎么办呢？

主分片的数目在索引创建时就已经确定了下来。实际上，这个数目定义了这个索引能够 存储 的最大数据量。（实际大小取决于你的数据、硬件和使用场景。） 但是，读操作—— 搜索和返回数据——可以同时被主分片 或 副本分片所处理，所以当你拥有越多的副本分片 时，也将拥有越高的吞吐量。

在运行中的集群上是可以动态调整副本分片数目的，我们可以按需伸缩集群。让我们把 副本数从默认的 1 增加到 2

```
 { "number_of_replicas" : 2 }
```

users 索引现在拥有 9 个分片：3 个主分片和 6 个副本分片。 这意味着我们可以将集群 扩容到 9 个节点，每个节点上一个分片。相比原来 3 个节点时，集群搜索性能可以提升 3 倍。

当然，如果只是在相同节点数目的集群上增加更多的副本分片并不能提高性能，因为每 个分片从节点上获得的资源会变少。 你需要增加更多的硬件资源来提升吞吐量。 但是更多的副本分片数提高了数据冗余量：按照上面的节点配置，我们可以在失去 2 个节点 的情况下不丢失任何数据。

####  应对故障

我们关闭第一个节点，这时集群的状态为:关闭了一个节点后的集群。

我们关闭的节点是一个主节点。而集群必须拥有一个主节点来保证正常工作，所以发生 的第一件事情就是选举一个新的主节点： Node 2 。在我们关闭 Node 1 的同时也失去了主 分片 1 和 2 ，并且在缺失主分片的时候索引也不能正常工作。 如果此时来检查集群的状况，我们看到的状态将会为 red ：不是所有主分片都在正常工作。

幸运的是，在其它节点上存在着这两个主分片的完整副本， 所以新的主节点立即将这 些分片在 Node 2 和 Node 3 上对应的副本分片提升为主分片， 此时集群的状态将会为 yellow。这个提升主分片的过程是瞬间发生的，如同按下一个开关一般。

**为什么我们集群状态是 yellow 而不是 green 呢？**

虽然我们拥有所有的三个主分片，但是同时设置了每个主分片需要对应 2 份副本分片，而此 时只存在一份副本分片。 所以集群不能为 green 的状态，不过我们不必过于担心：如果我 们同样关闭了 Node 2 ，我们的程序 依然 可以保持在不丢任何数据的情况下运行，因为 Node 3 为每一个分片都保留着一份副本。

如果我们重新启动 Node 1 ，集群可以将缺失的副本分片再次进行分配，那么集群的状 态也将恢复成之前的状态。 如果 Node 1 依然拥有着之前的分片，它将尝试去重用它们， 同时仅从主分片复制发生了修改的数据文件。和之前的集群相比，只是 Master 节点切换了。

#### 路由计算

当索引一个文档的时候，文档会被存储到一个主分片中。 Elasticsearch 如何知道一个 文档应该存放到哪个分片中呢？当我们创建文档时，它如何决定这个文档应当被存储在分片 1 还是分片 2 中呢？首先这肯定不会是随机的，否则将来要获取文档的时候我们就不知道 从何处寻找了。实际上，这个过程是根据下面这个公式决定的：

![image-20220218175241941](D:/mdimgs/image-20220218175241941.png)

routing 是一个可变值，默认是文档的 _id ，也可以设置成一个自定义的值。 routing 通过 hash 函数生成一个数字，然后这个数字再除以 number_of_primary_shards （主分片的数量）后得到余数 。这个分布在 0 到 number_of_primary_shards-1 之间的余数，就是我们所寻求 的文档所在分片的位置。

这就解释了为什么我们要在创建索引的时候就确定好主分片的数量 并且永远不会改变 这个数量：因为如果数量变化了，那么所有之前路由的值都会无效，文档也再也找不到了。

所有的文档 API（ get 、 index 、 delete 、 bulk 、 update 以及 mget ）都接受一 个叫做 routing 的路由参数 ，通过这个参数我们可以自定义文档到分片的映射。一个自定 义的路由参数可以用来确保所有相关的文档——例如所有属于同一个用户的文档——都被 存储到同一个分片中。

#### 分片控制

我们假设有一个集群由三个节点组成。 它包含一个叫 emps 的索引，有两个主分片， 每个主分片有两个副本分片。相同分片的副本不会放在同一节点。

我们可以发送请求到集群中的任一节点。 每个节点都有能力处理任意请求。 每个节点都知 道集群中任一文档位置，所以可以直接将请求转发到需要的节点上。 在下面的例子中，将 所有的请求发送到 Node 1，我们将其称为 协调节点(coordinating node) 。

当发送请求的时候， 为了扩展负载，更好的做法是轮询集群中所有的节点。

#### 写流程

新建、索引和删除 请求都是 写 操作， 必须在主分片上面完成之后才能被复制到相关 的副本分片

![image-20220218175407335](D:/mdimgs/image-20220218175407335.png)

新建，索引和删除文档所需要的步骤顺序：

1. 客户端向 Node 1 发送新建、索引或者删除请求。
2.  2节点使用文档的 _id 确定文档属于分片 0 。请求会被转发到 Node 3，因为分片 0 的 主分片目前被分配在 Node 3 上。 
3.  Node 3 在主分片上面执行请求。如果成功了，它将请求并行转发到 Node 1 和 Node 2  的副本分片上。一旦所有的副本分片都报告成功, Node 3 将向协调节点报告成功，协调 节点向客户端报告成功。 在客户端收到成功响应时，文档变更已经在主分片和所有副本分片执行完成，变更是安全的。 有一些可选的请求参数允许您影响这个过程，可能以数据安全为代价提升性能。这些选项很 少使用，因为 Elasticsearch 已经很快，但是为了完整起见，请参考下面表格：

参数 																														含义 

**consistency consistency，即一致性。**在默认设置下，即使仅仅是在试图执行一个_写_操作之 前，主分片都会要求 必须要有 规定数量(quorum)（或者换种说法，也即必须要 有大多数）的分片副本处于活跃可用状态，才会去执行_写_操作(其中分片副本 可以是主分片或者副本分片)。这是为了避免在发生网络分区故障（network  partition）的时候进行_写_操作，进而导致数据不一致。_规定数量_即： int( (primary + number_of_replicas) / 2 ) + 1 consistency 参数的值可以设为 one （只要主分片状态 ok 就允许执行_写_操 作）,all（必须要主分片和所有副本分片的状态没问题才允许执行_写_操作）, 或 quorum 。默认值为 quorum , 即大多数的分片副本状态没问题就允许执行_写_ 操作。 注意，规定数量 的计算公式中 number_of_replicas 指的是在索引设置中的设定 副本分片数，而不是指当前处理活动状态的副本分片数。如果你的索引设置中指定了当前索引拥有三个副本分片，那规定数量的计算结果即： int( (primary + 3 replicas) / 2 ) + 1 = 3 如果此时你只启动两个节点，那么处于活跃状态的分片副本数量就达不到规定数 量，也因此您将无法索引和删除任何文档。

 **timeout** 如果没有足够的副本分片会发生什么？ Elasticsearch 会等待，希望更多的分片出 现。默认情况下，它最多等待 1 分钟。 如果你需要，你可以使用 timeout 参数 使它更早终止： 100 100 毫秒，30s 是 30 秒。

**新索引默认有 1 个副本分片，这意味着为满足规定数量应该需要两个活动的分片副本。 但是，这些 默认的设置会阻止我们在单一节点上做任何事情。为了避免这个问题，要求只有当 number_of_replicas 大 于 1 的时候，规定数量才会执行。**

#### 读流程

![image-20220218175602390](D:/mdimgs/image-20220218175602390.png)

从主分片或者副本分片检索文档的步骤顺序： 

1. 客户端向 Node 1 发送获取请求。 
2. 节点使用文档的 _id 来确定文档属于分片 0 。分片 0 的副本分片存在于所有的三个 节点上。 在这种情况下，它将请求转发到 Node 2 。
3.  Node 2 将文档返回给 Node 1 ，然后将文档返回给客户端。 在处理读取请求时，协调结点在每次请求的时候都会通过轮询所有的副本分片来达到负载均 衡。在文档被检索时，已经被索引的文档可能已经存在于主分片上但是还没有复制到副本分 片。 在这种情况下，副本分片可能会报告文档不存在，但是主分片可能成功返回文档。 一 旦索引请求成功返回给用户，文档在主分片和副本分片都是可用的。

#### 更新流程

部分更新一个文档结合了先前说明的读取和写入流程：

![image-20220218175652503](D:/mdimgs/image-20220218175652503.png)

部分更新一个文档的步骤如下：

1. 客户端向 Node 1 发送更新请求。 

2. 它将请求转发到主分片所在的 Node 3 。 

3. Node 3 从主分片检索文档，修改 _source 字段中的 JSON ，并且尝试重新索引主分片 的文档。如果文档已经被另一个进程修改，它会重试步骤 3 ，超过 retry_on_conflict 次 后放弃。 

4.  如果 Node 3 成功地更新文档，它将新版本的文档并行转发到 Node 1 和 Node 2 上的 副本分片，重新建立索引。一旦所有副本分片都返回成功， Node 3 向协调节点也返回 成功，协调节点向客户端返回成功。

   **当主分片把更改转发到副本分片时， 它不会转发更新请求。 相反，它转发完整文档的新版本。请记住， 这些更改将会异步转发到副本分片，并且不能保证它们以发送它们相同的顺序到达。 如果 Elasticsearch 仅 转发更改请求，则可能以错误的顺序应用更改，导致得到损坏的文档。**

#### 多文档操作流程

mget 和 bulk API 的模式类似于单文档模式。区别在于协调节点知道每个文档存在于 哪个分片中。它将整个多文档请求分解成 每个分片 的多文档请求，并且将这些请求并行转 发到每个参与节点。

 协调节点一旦收到来自每个节点的应答，就将每个节点的响应收集整理成单个响应，返 回给客户端

![image-20220218175807972](D:/mdimgs/image-20220218175807972.png)

**用单个 mget 请求取回多个文档所需的步骤顺序:**

1. 客户端向 Node 1 发送 mget 请求

2. Node 1 为每个分片构建多文档获取请求，然后并行转发这些请求到托管在每个所需的 主分片或者副本分片的节点上。一旦收到所有答复， Node 1 构建响应并将其返回给客 户端。

   可以对 docs 数组中每个文档设置 routing 参数。 bulk API， 允许在单个批量请求中执行多个创建、索引、删除和更新请求。

   ![image-20220218175850359](D:/mdimgs/image-20220218175850359.png)

   bulk API 按如下步骤顺序执行： 

   1. 客户端向 Node 1 发送 bulk 请求。 
   2.  Node 1 为每个节点创建一个批量请求，并将这些请求并行转发到每个包含主分片的节 点主机。 
   3.  主分片一个接一个按顺序执行每个操作。当每个操作成功时，主分片并行转发新文档（或 删除）到副本分片，然后执行下一个操作。 一旦所有的副本分片报告所有操作成功， 该节点将向协调节点报告成功，协调节点将这些响应收集整理并返回给客户端。

#### 分片原理

分片是 Elasticsearch 最小的工作单元。但是究竟什么是一个分片，它是如何工作的？

 传统的数据库每个字段存储单个值，但这对全文检索并不够。文本字段中的每个单词需 要被搜索，对数据库意味着需要单个字段有索引多值的能力。最好的支持是一个字段多个值 需求的数据结构是倒排索引。

##### 倒排索引

Elasticsearch 使用一种称为倒排索引的结构，它适用于快速的全文搜索。 

见其名，知其意，有倒排索引，肯定会对应有正向索引。正向索引（forward index）， 反向索引（inverted index）更熟悉的名字是倒排索引。 

所谓的正向索引，就是搜索引擎会将待搜索的文件都对应一个文件 ID，搜索时将这个 ID 和搜索关键字进行对应，形成 K-V 对，然后对关键字进行统计计数。

但是互联网上收录在搜索引擎中的文档的数目是个天文数字，这样的索引结构根本无法满足 实时返回排名结果的要求。所以，搜索引擎会将正向索引重新构建为倒排索引，即把文件 ID对应到关键词的映射转换为关键词到文件ID的映射，每个关键词都对应着一系列的文件， 这些文件中都出现这个关键词。

个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文 档列表。例如，假设我们有两个文档，每个文档的 content 域包含如下内容：

  The quick brown fox jumped over the lazy dog 

 Quick brown foxes leap over lazy dogs in summer 

为了创建倒排索引，我们首先将每个文档的 content 域拆分成单独的 词（我们称它为 词条 或 tokens ），创建一个包含所有不重复词条的排序列表，然后列出每个词条出现在哪个文 档。结果如下所示：

![image-20220218180059130](D:/mdimgs/image-20220218180059130.png)

现在，如果我们想搜索 quick brown ，我们只需要查找包含每个词条的文档：

![image-20220218180140763](D:/mdimgs/image-20220218180140763.png)

两个文档都匹配，但是第一个文档比第二个匹配度更高。如果我们使用仅计算匹配词条数量 的简单相似性算法，那么我们可以说，对于我们查询的相关性来讲，第一个文档比第二个文 档更佳。

但是，我们目前的倒排索引有一些问题：

 Quick 和 quick 以独立的词条出现，然而用户可能认为它们是相同的词。

 fox 和 foxes 非常相似, 就像 dog 和 dogs ；他们有相同的词根。 

 jumped 和 leap, 尽管没有相同的词根，但他们的意思很相近。他们是同义词。

使用前面的索引搜索 +Quick +fox 不会得到任何匹配文档。（记住，+ 前缀表明这个词必 须存在。）只有同时出现 Quick 和 fox 的文档才满足这个查询条件，但是第一个文档包含 quick fox ，第二个文档包含 Quick foxes 。

我们的用户可以合理的期望两个文档与查询匹配。我们可以做的更好。 如果我们将词条规范为标准模式，那么我们可以找到与用户搜索的词条不完全一致，但具有 足够相关性的文档。例如：

 Quick 可以小写化为 quick 。

  foxes 可以 词干提取 --变为词根的格式-- 为 fox 。类似的， dogs 可以为提取为 dog 。 

 jumped 和 leap 是同义词，可以索引为相同的单词 jump 。

现在索引看上去像这样：

![image-20220218180236728](D:/mdimgs/image-20220218180236728.png)

这还远远不够。我们搜索 +Quick +fox 仍然 会失败，因为在我们的索引中，已经没有 Quick  了。但是，如果我们对搜索的字符串使用与 content 域相同的标准化规则，会变成查询 +quick +fox，这样两个文档都会匹配！分词和标准化的过程称为**分析**

这非常重要。你只能搜索在索引中出现的词条，所以索引文本和查询字符串必须标准化为相 同的格式。

##### 文档搜索

早期的全文检索会为整个文档集合建立一个很大的倒排索引并将其写入到磁盘。 一旦 新的索引就绪，旧的就会被其替换，这样最近的变化便可以被检索到。 **倒排索引被写入磁盘后是 不可改变 的:它永远不会修改。**

不变性有重要的价值： 

 不需要锁。如果你从来不更新索引，你就不需要担心多进程同时修改数据的问题。

  一旦索引被读入内核的文件系统缓存，便会留在哪里，由于其不变性。只要文件系统缓存中还有足够 的空间，那么大部分读请求会直接请求内存，而不会命中磁盘。这提供了很大的性能提升。 

 其它缓存(像 filter 缓存)，在索引的生命周期内始终有效。它们不需要在每次数据改变时被重建，因为 数据不会变化。 

 写入单个大的倒排索引允许数据被压缩，减少磁盘 I/O 和 需要被缓存到内存的索引的使用量。 

当然，一个不变的索引也有不好的地方。主要事实是它是不可变的! 你不能修改它。如 果你需要让一个新的文档 可被搜索，你需要重建整个索引。这要么对一个索引所能包含的 数据量造成了很大的限制，要么对索引可被更新的频率造成了很大的限制。

##### 动态更新索引

如何在保留不变性的前提下实现倒排索引的更新？

答案是: 用更多的索引。通过增加新的补充索引来反映新近的修改，而不是直接重写整 个倒排索引。每一个倒排索引都会被轮流查询到，从最早的开始查询完后再对结果进行合并。 

Elasticsearch 基于 Lucene, 这个 java 库引入了**按段搜索**的概念。 每一 段 本身都是一 个倒排索引， 但索引在 Lucene 中除表示所有段的集合外， 还增加了提交点的概念 — 一 个列出了所有已知段的文件。

![image-20220218183737078](D:/mdimgs/image-20220218183737078.png)

当一个查询被触发，所有已知的段按顺序被查询。词项统计会对所有段的结果进行聚合，以 保证每个词和每个文档的关联都被准确计算。 这种方式可以用相对较低的成本将新文档添 加到索引。 段是不可改变的，所以既不能从把文档从旧的段中移除，也不能修改旧的段来进行反映文档 的更新。 取而代之的是，每个提交点会包含一个 .del 文件，文件中会列出这些被删除文档 的段信息。 

当一个文档被 “删除” 时，它实际上只是在 .del 文件中被 标记 删除。一个被标记删除的文档仍然可以被查询匹配到， 但它会在最终结果被返回前从结果集中移除。 

文档更新也是类似的操作方式：当一个文档被更新时，旧版本文档被标记删除，文档的新版 本被索引到一个新的段中。 可能两个版本的文档都会被一个查询匹配到，但被删除的那个 旧版本文档在结果集返回前就已经被移除。

##### 近实时搜索

随着按段（per-segment）搜索的发展，一个新的文档从索引到可被搜索的延迟显著降低 了。新文档在几分钟之内即可被检索，但这样还是不够快。磁盘在这里成为了瓶颈。提交 （Commiting）一个新的段到磁盘需要一个 fsync 来确保段被物理性地写入磁盘，这样在断 电的时候就不会丢失数据。 但是 fsync 操作代价很大; 如果每次索引一个文档都去执行一 次的话会造成很大的性能问题。 

我们需要的是一个更轻量的方式来使一个文档可被搜索，这意味着 fsync 要从整个过程中 被移除。在 Elasticsearch 和磁盘之间是文件系统缓存。 像之前描述的一样， 在内存索引缓 冲区中的文档会被写入到一个新的段中。 但是这里新段会被先写入到文件系统缓存—这一 步代价会比较低，稍后再被刷新到磁盘—这一步代价比较高。不过只要文件已经在缓存中， 就可以像其它文件一样被打开和读取了。

![image-20220218183859558](D:/mdimgs/image-20220218183859558.png)

Lucene 允许新段被写入和打开—使其包含的文档在未进行一次完整提交时便对搜索可见。 这种方式比进行一次提交代价要小得多，并且在不影响性能的前提下可以被频繁地执行

![image-20220218183915004](D:/mdimgs/image-20220218183915004.png)

在 Elasticsearch 中，写入和打开一个新段的轻量的过程叫做 refresh 。 默认情况下每个分 片会每秒自动刷新一次。这就是为什么我们说 Elasticsearch 是 近 实时搜索: 文档的变化 并不是立即对搜索可见，但会在一秒之内变为可见。 

这些行为可能会对新用户造成困惑: 他们索引了一个文档然后尝试搜索它，但却没有搜到。 这个问题的解决办法是用 refresh API 执行一次手动刷新: /users/_refresh

**尽管刷新是比提交轻量很多的操作，它还是会有性能开销。当写测试的时候， 手动刷新很有用，但是不要 在生产环境下每次索引一个文档都去手动刷新。 相反，你的应用需要意识到 Elasticsearch 的近实时的性 质，并接受它的不足。** 

并不是所有的情况都需要每秒刷新。可能你正在使用 Elasticsearch 索引大量的日志文件， 你可能想优化索引速度而不是近实时搜索， 可以通过设置 refresh_interval ， 降低每个索 引的刷新频率

```
{
 "settings": {
 "refresh_interval": "30s" 
 }
}
```

refresh_interval 可以在既存索引上进行动态更新。 在生产环境中，当你正在建立一个大的 新索引时，可以先关闭自动刷新，待开始使用该索引时，再把它们调回来 

```
# 关闭自动刷新 PUT /users/_settings { "refresh_interval": -1 }  # 每一秒刷新 PUT /users/_settings { "refresh_interval": "1s" } 
```

##### 持久化变更

如果没有用 fsync 把数据从文件系统缓存刷（flush）到硬盘，我们不能保证数据在断 电甚至是程序正常退出之后依然存在。为了保证 Elasticsearch 的可靠性，需要确保数据变 化被持久化到磁盘。在 动态更新索引，我们说一次完整的提交会将段刷到磁盘，并写入一个包含所有段列表的提交点。Elasticsearch 在启动或重新打开一个索引的过程中使用这个提 交点来判断哪些段隶属于当前分片。

即使通过每秒刷新（refresh）实现了近实时搜索，我们仍然需要经常进行完整提交来确 保能从失败中恢复。但在两次提交之间发生变化的文档怎么办？我们也不希望丢失掉这些数 据。Elasticsearch 增加了一个 translog ，或者叫事务日志，在每一次对 Elasticsearch 进行 操作时均进行了日志记录

![image-20220218184109940](D:/mdimgs/image-20220218184109940.png)

![image-20220218184123725](D:/mdimgs/image-20220218184123725.png)

![image-20220218184142316](D:/mdimgs/image-20220218184142316.png)

你很少需要自己手动执行 flush 操作；通常情况下，自动刷新就足够了。这就是说，在 重启节点或关闭索引之前执行 flush 有益于你的索引。当 Elasticsearch 尝试恢复或重新打 开一个索引， 它需要重放 translog 中所有的操作，所以如果日志越短，恢复越快。

translog 的目的是保证操作不会丢失，在文件被 fsync 到磁盘前，被写入的文件在重启 之后就会丢失。默认 translog 是每 5 秒被 fsync 刷新到硬盘， 或者在每次写请求完成之 后执行(e.g. index, delete, update, bulk)。这个过程在主分片和复制分片都会发生。最终， 基 本上，这意味着在整个请求被 fsync 到主分片和复制分片的 translog 之前，你的客户端不会 得到一个 200 OK 响应。

在每次请求后都执行一个 fsync 会带来一些性能损失，尽管实践表明这种损失相对较 小（特别是 bulk 导入，它在一次请求中平摊了大量文档的开销）。

但是对于一些大容量的偶尔丢失几秒数据问题也并不严重的集群，使用异步的 fsync  还是比较有益的。比如，写入的数据被缓存到内存中，再每 5 秒执行一次 fsync 。如果你 决定使用异步 translog 的话，你需要 保证 在发生 crash 时，丢失掉 sync_interval 时间段 的数据也无所谓。请在决定前知晓这个特性。如果你不确定这个行为的后果，最好是使用默 认的参数（ "index.translog.durability": "request" ）来避免数据丢失。

##### 段合并

由于自动刷新流程每秒会创建一个新的段 ，这样会导致短时间内的段数量暴增。而段 数目太多会带来较大的麻烦。 每一个段都会消耗文件句柄、内存和 cpu 运行周期。更重要 的是，每个搜索请求都必须轮流检查每个段；所以段越多，搜索也就越慢。 Elasticsearch 通过在后台进行段合并来解决这个问题。小的段被合并到大的段，然后这些大 的段再被合并到更大的段。

段合并的时候会将那些旧的已删除文档从文件系统中清除。被删除的文档（或被更新文档的 旧版本）不会被拷贝到新的大段中。

启动段合并不需要你做任何事。进行索引和搜索时会自动进行。

1. 当索引的时候，刷新（refresh）操作会创建新的段并将段打开以供搜索使用。 

2. 合并进程选择一小部分大小相似的段，并且在后台将它们合并到更大的段中。这并不会 中断索引和搜索

   ![image-20220218184251487](D:/mdimgs/image-20220218184251487.png)

   3.一旦合并结束，老的段被删除 

    新的段被刷新（flush)到了磁盘。 ** 写入一个包含新段且排除旧的和较小的段 的新提交点。 

    新的段被打开用来搜索。 

    老的段被删除。

   ![image-20220218184342596](D:/mdimgs/image-20220218184342596.png)

#### 文档分析

```
分析 包含下面的过程：
 将一块文本分成适合于倒排索引的独立的 词条
 将这些词条统一化为标准格式以提高它们的“可搜索性”，或者 recall分析器执行上面的工作。分析器实际上是将三个功能封装到了一个包里：
 字符过滤器
首先，字符串按顺序通过每个 字符过滤器 。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉 HTML，或者将 & 转化成 and。
 分词器
其次，字符串被 分词器 分为单个的词条。一个简单的分词器遇到空格和标点的时候，可能会将文本拆分成词条。
 Token 过滤器
最后，词条按顺序通过每个 token 过滤器 。这个过程可能会改变词条（例如，小写化Quick ），删除词条（例如， 像 a， and， the 等无用词），或者增加词条（例如，像 jump 和 leap 这种同义词）。
```

##### 内置分析器

```
Elasticsearch 还附带了可以直接使用的预包装的分析器。接下来我们会列出最重要的分析器。为了证明它们的差异，我们看看每个分析器会从下面的字符串得到哪些词条：
"Set the shape to semi-transparent by calling set_trans(5)"
 标准分析器
标准分析器是 Elasticsearch 默认使用的分析器。它是分析各种语言文本最常用的选择。它根据 Unicode 联盟 定义的 单词边界 划分文本。删除绝大部分标点。最后，将词条小写。它会产生：set, the, shape, to, semi, transparent, by, calling, set_trans, 5
 简单分析器
简单分析器在任何不是字母的地方分隔文本，将词条小写。它会产生：set, the, shape, to, semi, transparent, by, calling, set, trans
 空格分析器
空格分析器在空格的地方划分文本。它会产生：Set, the, shape, to, semi-transparent, by, calling, set_trans(5)
 语言分析器
特定语言分析器可用于 很多语言。它们可以考虑指定语言的特点。例如， 英语 分析器附带了一组英语无用词（常用单词，例如 and 或者 the ，它们对相关性没有多少影响），它们会被删除。 由于理解英语语法的规则，这个分词器可以提取英语单词的 词干 。英语 分词器会产生下面的词条：set, shape, semi, transpar, call, set_tran, 5注意看 transparent、 calling 和 set_trans 已经变为词根格式
```

##### 分析器使用场景

```
当我们 索引 一个文档，它的全文域被分析成词条以用来创建倒排索引。 但是，当我们在全文域 搜索 的时候，我们需要将查询字符串通过 相同的分析过程 ，以保证我们搜索的词条格式与索引中的词条格式一致。
全文查询，理解每个域是如何定义的，因此它们可以做正确的事：
 当你查询一个 全文 域时， 会对查询字符串应用相同的分析器，以产生正确的搜索词条列表。
 当你查询一个 精确值 域时，不会分析查询字符串，而是搜索你指定的精确值。
```

##### 测试分析器

有些时候很难理解分词的过程和实际被存储到索引中的词条，特别是你刚接触 Elasticsearch。为了理解发生了什么，你可以使用 analyze API 来看文本是如何被分析的。 在消息体里，指定分析器和要分析的文本

```
GET http://localhost:9200/_analyze
{
 "analyzer": "standard",
 "text": "Text to analyze"
}
```

结果中每个元素代表一个单独的词条：

```
{
 "tokens": [
 {
 "token": "text",
 "start_offset": 0,
 "end_offset": 4,
 "type": "<ALPHANUM>",
 "position": 1
 },
 {
 "token": "to",
 "start_offset": 5,
 "end_offset": 7,
 "type": "<ALPHANUM>",
 "position": 2
 },
 {
 "token": "analyze",
 "start_offset": 8,
 "end_offset": 15,
 "type": "<ALPHANUM>",
 "position": 3
 }
 ]
}
```

token 是实际存储到索引中的词条。 position 指明词条在原始文本中出现的位置。 start_offset 和 end_offset 指明字符在原始字符串中的位置。

##### 指定分析器

当Elasticsearch在你的文档中检测到一个新的字符串域，它会自动设置其为一个全文 字 符串 域，使用 标准 分析器对它进行分析。你不希望总是这样。可能你想使用一个不同的 分析器，适用于你的数据使用的语言。有时候你想要一个字符串域就是一个字符串域—不使用分析，直接索引你传入的精确值，例如用户 ID 或者一个内部的状态域或标签。要做到这 一点，我们必须手动指定这些域的映射。

##### IK 分词器

```
首先我们通过 Postman 发送 GET 请求查询分词效果
ES 的默认分词器无法识别中文中测试、单词这样的词汇，而是简单的将每个字拆完分为一个词
```

```
{
 "tokens": [
 {
 "token": "测",
 "start_offset": 0,
 "end_offset": 1,
 "type": "<IDEOGRAPHIC>",
 "position": 0
 },
 {
 "token": "试",
 "start_offset": 1,
 "end_offset": 2,
 "type": "<IDEOGRAPHIC>",
 "position": 1
 },
 {
 "token": "单",
 "start_offset": 2,
 "end_offset": 3,
 "type": "<IDEOGRAPHIC>",
 "position": 2
 },
 {
 "token": "词",
 "start_offset": 3,
 "end_offset": 4,
 "type": "<IDEOGRAPHIC>",
 "position": 3
 }
 ]
}
```

这样的结果显然不符合我们的使用要求，所以我们需要下载 ES 对应版本的中文分词器

```
我们这里采用 IK 中文分词器，下载地址为: 
https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.8.0
将解压后的后的文件夹放入 ES 根目录下的 plugins 目录下，重启 ES 即可使用。
我们这次加入新的查询参数"analyzer":"ik_max_word"
# GET http://localhost:9200/_analyze
{
"text":"测试单词",
"analyzer":"ik_max_word"
}
 ik_max_word：会将文本做最细粒度的拆分
 ik_smart：会将文本做最粗粒度的拆分
```

```
使用中文分词后的结果为：
{
 "tokens": [
 {
 "token": "测试",
 "start_offset": 0,
 "end_offset": 2,
 "type": "CN_WORD",
 "position": 0
 },
 {
 "token": "单词",
 "start_offset": 2,
 "end_offset": 4,
 "type": "CN_WORD",
 "position": 1
 }
 ]
}
```

```
ES 中也可以进行扩展词汇，首先查询
# GET http://localhost:9200/_analyze
{
"text":"弗雷尔卓德",
"analyzer":"ik_max_word"
}
仅仅可以得到每个字的分词结果，我们需要做的就是使分词器识别到弗雷尔卓德也是一个词
语
{
 "tokens": [
 {
"token": "弗",
 "start_offset": 0,
 "end_offset": 1,
 "type": "CN_CHAR",
 "position": 0
 },
 {
 "token": "雷",
 "start_offset": 1,
 "end_offset": 2,
 "type": "CN_CHAR",
 "position": 1
 },
 {
 "token": "尔",
 "start_offset": 2,
 "end_offset": 3,
 "type": "CN_CHAR",
 "position": 2
 },
 {
 "token": "卓",
 "start_offset": 3,
 "end_offset": 4,
 "type": "CN_CHAR",
 "position": 3
 },
 {
 "token": "德",
 "start_offset": 4,
 "end_offset": 5,
 "type": "CN_CHAR",
 "position": 4
 }
 ]
}
```

```
首先进入 ES 根目录中的 plugins 文件夹下的 ik 文件夹，进入 config 目录，创建 custom.dic文件，写入弗雷尔卓德。同时打开 IKAnalyzer.cfg.xml 文件，将新建的 custom.dic 配置其中，重启 ES 服务器。
```

##### 自定义分析器

```
虽然 Elasticsearch 带有一些现成的分析器，然而在分析器上 Elasticsearch 真正的强大之·处在于，你可以通过在一个适合你的特定数据的设置之中组合字符过滤器、分词器、词汇单元过滤器来创建自定义的分析器。在 分析与分析器 我们说过，一个 分析器 就是在一个包里面组合了三种函数的一个包装器， 三种函数按照顺序被执行:
 字符过滤器
字符过滤器 用来 整理 一个尚未被分词的字符串。例如，如果我们的文本是 HTML 格式的，它会包含像 <p> 或者 <div> 这样的 HTML 标签，这些标签是我们不想索引的。我们可以使用 html 清除 字符过滤器 来移除掉所有的 HTML 标签，并且像把 &Aacute; 转换为相对应的 Unicode 字符 Á 这样，转换 HTML 实体。一个分析器可能有 0 个或者多个字符过滤器。
 分词器
一个分析器 必须 有一个唯一的分词器。 分词器把字符串分解成单个词条或者词汇单元。 标准 分析器里使用的 标准 分词器 把一个字符串根据单词边界分解成单个词条，并且移除掉大部分的标点符号，然而还有其他不同行为的分词器存在。例如， 关键词 分词器 完整地输出 接收到的同样的字符串，并不做任何分词。 空格 分词器 只根据空格分割文本 。 正则 分词器 根据匹配正则表达式来分割文本 。
 词单元过滤器
经过分词，作为结果的 词单元流 会按照指定的顺序通过指定的词单元过滤器 。词单元过滤器可以修改、添加或者移除词单元。我们已经提到过 lowercase 和 stop 词过滤器 ，但是在 Elasticsearch 里面还有很多可供选择的词单元过滤器。词干过滤器 把单词 遏制为词干。 ascii_folding 过滤器移除变音符，把一个像 "très" 这样的词转换为 "tres" 。
```

```
ngram 和 edge_ngram 词单元过滤器 可以产生 适合用于部分匹配或者自动补全的词单元。
接下来，我们看看如何创建自定义的分析器：
# PUT http://localhost:9200/my_index
{
 "settings": {
 "analysis": {
 "char_filter": {
 "&_to_and": {
 "type": "mapping",
 "mappings": [ "&=> and "]
 }},
 "filter": {
 "my_stopwords": {
 "type": "stop",
 "stopwords": [ "the", "a" ]
 }},
 "analyzer": {
 "my_analyzer": {
 "type": "custom",
 "char_filter": [ "html_strip", "&_to_and" ],
 "tokenizer": "standard",
 "filter": [ "lowercase", "my_stopwords" ]
 }}
}}}
```

```
索引被创建以后，使用 analyze API 来 测试这个新的分析器
# GET http://127.0.0.1:9200/my_index/_analyze
{
 "text":"The quick & brown fox",
 "analyzer": "my_analyzer"
}
```

```
下面的缩略结果展示出我们的分析器正在正确地运行
{
 "tokens": [
 {
 "token": "quick",
 "start_offset": 4,
 "end_offset": 9,
 "type": "<ALPHANUM>",
 "position": 1
 },
 {
 "token": "and",
 "start_offset": 10,
 "end_offset": 11,
 "type": "<ALPHANUM>",
 "position": 2
 },
 {"token": "brown",
 "start_offset": 12,
 "end_offset": 17,
 "type": "<ALPHANUM>",
 "position": 3
 },
 {
 "token": "fox",
 "start_offset": 18,
 "end_offset": 21,
 "type": "<ALPHANUM>",
 "position": 4
 }
 ]
}
```

#### 文档处理

##### 文档冲突

当我们使用 index API 更新文档 ，可以一次性读取原始文档，做我们的修改，然后重 新索引 整个文档 。 最近的索引请求将获胜：无论最后哪一个文档被索引，都将被唯一存 储在 Elasticsearch 中。如果其他人同时更改这个文档，他们的更改将丢失。 很多时候这是没有问题的。也许我们的主数据存储是一个关系型数据库，我们只是将数 据复制到 Elasticsearch 中并使其可被搜索。 也许两个人同时更改相同的文档的几率很小。 或者对于我们的业务来说偶尔丢失更改并不是很严重的问题。 但有时丢失了一个变更就是 非常严重的 。试想我们使用 Elasticsearch 存储我们网上 商城商品库存的数量， 每次我们卖一个商品的时候，我们在 Elasticsearch 中将库存数量减 少。有一天，管理层决定做一次促销。突然地，我们一秒要卖好几个商品。 假设有两个 web  程序并行运行，每一个都同时处理所有商品的销售

![image-20220218192602268](D:/mdimgs/image-20220218192602268.png)

web_1 对 stock_count 所做的更改已经丢失，因为 web_2 不知道它的 stock_count 的 拷贝已经过期。 结果我们会认为有超过商品的实际数量的库存，因为卖给顾客的库存商品并不存在，我们将让他们非常失望。 变更越频繁，读数据和更新数据的间隙越长，也就越可能丢失变更。 在数据库领域中，有两种方法通常被用来确保并发更新时变更不会丢失：

##### 悲观并发控制

这种方法被关系型数据库广泛使用，它假定有变更冲突可能发生，因此阻塞访问资源以 防止冲突。 一个典型的例子是读取一行数据之前先将其锁住，确保只有放置锁的线程能够 对这行数据进行修改。

##### 乐观并发控制

Elasticsearch 中使用的这种方法假定冲突是不可能发生的，并且不会阻塞正在尝试的操 作。 然而，如果源数据在读写当中被修改，更新将会失败。应用程序接下来将决定该如何 解决冲突。 例如，可以重试更新、使用新的数据、或者将相关情况报告给用户。

Elasticsearch 是分布式的。当文档创建、更新或删除时， 新版本的文档必须复制到集 群中的其他节点。Elasticsearch 也是异步和并发的，这意味着这些复制请求被并行发送，并 且到达目的地时也许 顺序是乱的 。 Elasticsearch 需要一种方法确保文档的旧版本不会覆 盖新的版本。

当我们之前讨论 index ，GET 和 delete 请求时，我们指出每个文档都有一个 _version  （版本）号，当文档被修改时版本号递增。 Elasticsearch 使用这个 version 号来确保变更 以正确顺序得到执行。如果旧版本的文档在新版本之后到达，它可以被简单的忽略。

我们可以利用 version 号来确保 应用中相互冲突的变更不会导致数据丢失。我们通过 指定想要修改文档的 version 号来达到这个目的。 如果该版本不是当前版本号，我们的请 求将会失败。

老的版本 es 使用 version，但是新版本不支持了，会报下面的错误，提示我们用 if_seq_no 和 if_primary_term

```
{
 "error": {
 "root_cause": [
 {
 "type": "action_request_validation_exception",
 "reason": "Validation Failed: 1: internal versioning can not be used 
for optimistic concurrency control. Please use `if_seq_no` and `if_primary_term` 
instead;"
 }
 ],
 "type": "action_request_validation_exception",
"reason": "Validation Failed: 1: internal versioning can not be used for 
optimistic concurrency control. Please use `if_seq_no` and `if_primary_term` 
instead;"
 },
 "status": 400
}
```

##### 外部系统版本控制

一个常见的设置是使用其它数据库作为主要的数据存储，使用 Elasticsearch 做数据检 索， 这意味着主数据库的所有更改发生时都需要被复制到 Elasticsearch ，如果多个进程负 责这一数据同步，你可能遇到类似于之前描述的并发问题。

如果你的主数据库已经有了版本号 — 或一个能作为版本号的字段值比如 timestamp — 那么你就可以在 Elasticsearch 中通过增加 version_type=external 到查询字符串的方式重用 这些相同的版本号， 版本号必须是大于零的整数， 且小于 9.2E+18 — 一个 Java 中 long  类型的正值。

外部版本号的处理方式和我们之前讨论的内部版本号的处理方式有些不同， Elasticsearch 不是检查当前 _version 和请求中指定的版本号是否相同， 而是检查当前 _version 是否 小于 指定的版本号。 如果请求成功，外部的版本号作为文档的新 _version  进行存储。

外部版本号不仅在索引和删除请求是可以指定，而且在 创建 新文档时也可以指定。

#### Kibana

[Kibana：数据的探索、可视化和分析 | Elastic](https://www.elastic.co/cn/kibana/)

```
修改 config/kibana.yml 文件
# 默认端口
server.port: 5601
# ES 服务器的地址
elasticsearch.hosts: ["http://localhost:9200"]
# 索引名
kibana.index: ".kibana"
# 支持中文
i18n.locale: "zh-CN"
```

## Elasticsearch 集成

#### springboot

##### **pom**

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.6.RELEASE</version>
        <relativePath/>
    </parent>
        <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-test</artifactId>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
        </dependency>
    </dependencies>
```

##### config

```java
@ConfigurationProperties(prefix = "elasticsearch")
@Configuration
@Data
public class ElasticsearchConfig extends AbstractElasticsearchConfiguration {
    private String host ;
    private Integer port ;

    //重写父类方法
    @Override
    public RestHighLevelClient elasticsearchClient() {
        RestClientBuilder builder = RestClient.builder(new HttpHost(host, port));
        RestHighLevelClient restHighLevelClient = new RestHighLevelClient(builder);
        return restHighLevelClient;
    }
}
```

##### yml

```properties
elasticsearch.host=127.0.0.1
elasticsearch.port=9200
logging.level.com.atguigu.es=debug
```

##### bean

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@Document(indexName = "product", shards = 3, replicas = 1)
public class Product {
    @Id
    private Long id;//商品唯一标识
    @Field(type = FieldType.Text)
    private String title;//商品名称
    @Field(type = FieldType.Keyword)
    private String category;//分类名称
    @Field(type = FieldType.Double)
    private Double price;//商品价格
    @Field(type = FieldType.Keyword, index = false)
    private String images;//图片地址
}
```

##### dao

```java
@Repository
public interface ProductDao extends ElasticsearchRepository<Product,Long> {
}
```

##### 索引操作

```java
 @Autowired
    private ElasticsearchRestTemplate elasticsearchRestTemplate;

    //创建索引并增加映射配置
    @Test
    public void createIndex(){
        System.out.println("创建索引");
    }

    @Test
    public void deleteIndex(){
        //创建索引，系统初始化会自动创建索引
        boolean flg = elasticsearchRestTemplate.deleteIndex(Product.class);
        System.out.println("删除索引 = " + flg);
    }
```

##### 文档操作

```java
@Autowired
 private ProductDao productDao;
 /**
 * 新增
 */
 @Test
 public void save(){
 Product product = new Product();
 product.setId(2L);
 product.setTitle("华为手机");
 product.setCategory("手机");
 product.setPrice(2999.0);
 product.setImages("http://www.atguigu/hw.jpg");
 productDao.save(product);
 }
 //修改
 @Test
 public void update(){
 Product product = new Product();
 product.setId(1L);
 product.setTitle("小米 2 手机");
 product.setCategory("手机");
 product.setPrice(9999.0);
 product.setImages("http://www.atguigu/xm.jpg");
 productDao.save(product);
 }
 //根据 id 查询
 @Test
 public void findById(){
 Product product = productDao.findById(1L).get();
 System.out.println(product);
 }
 //查询所有
@Test
 public void findAll(){
 Iterable<Product> products = productDao.findAll();
 for (Product product : products) {
 System.out.println(product);
 }
 }
 //删除
 @Test
 public void delete(){
 Product product = new Product();
 product.setId(1L);
 productDao.delete(product);
 }
 //批量新增
 @Test
 public void saveAll(){
 List<Product> productList = new ArrayList<>();
 for (int i = 0; i < 10; i++) {
 Product product = new Product();
 product.setId(Long.valueOf(i));
 product.setTitle("["+i+"]小米手机");
 product.setCategory("手机");
 product.setPrice(1999.0+i);
 product.setImages("http://www.atguigu/xm.jpg");
 productList.add(product);
 }
 productDao.saveAll(productList);
 }
 //分页查询
 @Test
 public void findByPageable(){
 //设置排序(排序方式，正序还是倒序，排序的 id)
 Sort sort = Sort.by(Sort.Direction.DESC,"id");
 int currentPage=0;//当前页，第一页从 0 开始，1 表示第二页
 int pageSize = 5;//每页显示多少条
 //设置查询分页
 PageRequest pageRequest = PageRequest.of(currentPage, pageSize,sort);
 //分页查询
 Page<Product> productPage = productDao.findAll(pageRequest);
 for (Product Product : productPage.getContent()) {
 System.out.println(Product);
 }
 }

```

##### 查询操作

```java
@Autowired
 private ProductDao productDao;
 /**
 * term 查询
 * search(termQueryBuilder) 调用搜索方法，参数查询构建器对象
 */
 @Test
 public void termQuery(){
 TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", "
小米");
 Iterable<Product> products = productDao.search(termQueryBuilder);
 for (Product product : products) {
 System.out.println(product);
 }
 }
 /**
 * term 查询加分页
 */
 @Test
 public void termQueryByPage(){
 int currentPage= 0 ;
 int pageSize = 5;
 //设置查询分页
 PageRequest pageRequest = PageRequest.of(currentPage, pageSize);
 TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", "
小米");
 Iterable<Product> products = 
productDao.search(termQueryBuilder,pageRequest);
 for (Product product : products) {
 System.out.println(product);
 }
 }
```

## Elasticsearch 优化

#### 硬件选择

```
Elasticsearch 的基础是 Lucene，所有的索引和文档数据是存储在本地的磁盘中，具体的 路径可在 ES 的配置文件../config/elasticsearch.yml 中配置，
#----------------------------------- Paths
------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
```

```
磁盘在现代服务器上通常都是瓶颈。Elasticsearch 重度使用磁盘，你的磁盘能处理的吞吐量越大，你的节点就越稳定。这里有一些优化磁盘 I/O 的技巧：
 使用 SSD。就像其他地方提过的， 他们比机械磁盘优秀多了。
 使用 RAID 0。条带化 RAID 会提高磁盘 I/O，代价显然就是当一块硬盘故障时整个就故障了。不要使用镜像或者奇偶校验 RAID 因为副本已经提供了这个功能。
 另外，使用多块硬盘，并允许 Elasticsearch 通过多个 path.data 目录配置把数据条带化分配到它们上面。
 不要使用远程挂载的存储，比如 NFS 或者 SMB/CIFS。这个引入的延迟对性能来说完全是背道而驰的。
```

#### 分片策略

##### 合理设置分片数

```
分片和副本的设计为 ES 提供了支持分布式和故障转移的特性，但并不意味着分片和副本是可以无限分配的。而且索引的分片完成分配后由于索引的路由机制，我们是不能重新修改分片数的。
可能有人会说，我不知道这个索引将来会变得多大，并且过后我也不能更改索引的大小，所以为了保险起见，还是给它设为 1000 个分片吧。但是需要知道的是，一个分片并不是没有代价的。需要了解：
 一个分片的底层即为一个 Lucene 索引，会消耗一定文件句柄、内存、以及 CPU 运转。
 每一个搜索请求都需要命中索引中的每一个分片，如果每一个分片都处于不同的节点还好， 但如多个分片都需要在同一个节点上竞争使用相同的资源就有些糟糕了。
 用于计算相关度的词项统计信息是基于分片的。如果有许多分片，每一个都只有很少的数据会导致很低的相关度。
个业务索引具体需要分配多少分片可能需要架构师和技术人员对业务的增长有个预先的判断，横向扩展应当分阶段进行。为下一阶段准备好足够的资源。 只有当你进入到下一个阶段，你才有时间思考需要作出哪些改变来达到这个阶段。一般来说，我们遵循一些原
则：
 控制每个分片占用的硬盘容量不超过 ES 的最大 JVM 的堆空间设置（一般设置不超过 32G，参考下文的 JVM 设置原则），因此，如果索引的总容量在 500G 左右，那分片大小在 16 个左右即可；当然，最好同时考虑原则 2。
 考虑一下 node 数量，一般一个节点有时候就是一台物理机，如果分片数过多，大大超过了节点数，很可能会导致一个节点上存在多个分片，一旦该节点故障，即使保持了 1 个以上的副本，同样有可能会导致数据丢失，集群无法恢复。所以， 一般都设置分片数不超过节点数的 3 倍。
 主分片，副本和节点最大数之间数量，我们分配的时候可以参考以下关系：节点数<=主分片数*（副本数+1）
```

##### 推迟分片分配

```
对于节点瞬时中断的问题，默认情况，集群会等待一分钟来查看节点是否会重新加入，如果这个节点在此期间重新加入，重新加入的节点会保持其现有的分片数据，不会触发新的分片分配。这样就可以减少 ES 在自动再平衡可用分片时所带来的极大开销。
通过修改参数 delayed_timeout ，可以延长再均衡的时间，可以全局设置也可以在索引级别进行修改:
PUT /_all/_settings 
{
 "settings": {
 "index.unassigned.node_left.delayed_timeout": "5m" 
 }
}

```

#### 路由选择

```
当我们查询文档的时候，Elasticsearch 如何知道一个文档应该存放到哪个分片中呢？它其实是通过下面这个公式来计算出来:
shard = hash(routing) % number_of_primary_shards
routing 默认值是文档的 id，也可以采用自定义值，比如用户 id。
不带 routing 查询
在查询的时候因为不知道要查询的数据具体在哪个分片上，所以整个过程分为 2 个步骤
 分发：请求到达协调节点后，协调节点将查询请求分发到每个分片上。
 聚合: 协调节点搜集到每个分片上查询结果，在将查询的结果进行排序，之后给用户返回结果。
带 routing 查询
查询的时候，可以直接根据 routing 信息定位到某个分配查询，不需要查询所有的分配，经过协调节点排序。
向上面自定义的用户查询，如果 routing 设置为 userid 的话，就可以直接查询出数据来，效率提升很多。
```

#### 写入速度优化

```
ES 的默认配置，是综合了数据可靠性、写入速度、搜索实时性等因素。实际使用时，我们需要根据公司要求，进行偏向性的优化。
针对于搜索性能要求不高，但是对写入要求较高的场景，我们需要尽可能的选择恰当写优化策略。综合来说，可以考虑以下几个方面来提升写索引的性能：
 加大 Translog Flush ，目的是降低 Iops、Writeblock。
 增加 Index Refresh 间隔，目的是减少 Segment Merge 的次数。
 调整 Bulk 线程池和队列。
 优化节点间的任务分布。
 优化 Lucene 层的索引建立，目的是降低 CPU 及 IO。
```

#####  批量数据提交

```
ES 提供了 Bulk API 支持批量操作，当我们有大量的写任务时，可以使用 Bulk 来进行批量写入。
通用的策略如下：Bulk 默认设置批量提交的数据量不能超过 100M。数据条数一般是根据文档的大小和服务器性能而定的，但是单次批处理的数据大小应从 5MB～15MB 逐渐增加，当性能没有提升时，把这个数据量作为最大值。
```

##### 优化存储设备

```
ES 是一种密集使用磁盘的应用，在段合并的时候会频繁操作磁盘，所以对磁盘要求较高，当磁盘速度提升之后，集群的整体性能会大幅度提高
```

##### 合理使用合并

```
Lucene 以段的形式存储数据。当有新的数据写入索引时，Lucene 就会自动创建一个新的段。
随着数据量的变化，段的数量会越来越多，消耗的多文件句柄数及 CPU 就越多，查询效率就会下降。
由于 Lucene 段合并的计算量庞大，会消耗大量的 I/O，所以 ES 默认采用较保守的策略，让后台定期进行段合并
```

#####  减少 Refresh 的次数

```
Lucene 在新增数据时，采用了延迟写入的策略，默认情况下索引的 refresh_interval 为1 秒。
Lucene 将待写入的数据先写到内存中，超过 1 秒（默认）时就会触发一次 Refresh，然后 Refresh 会把内存中的的数据刷新到操作系统的文件缓存系统中。
如果我们对搜索的实效性要求不高，可以将 Refresh 周期延长，例如 30 秒。这样还可以有效地减少段刷新次数，但这同时意味着需要消耗更多的 Heap 内存。
```

##### 加大 Flush 设置

```
Flush 的主要目的是把文件缓存系统中的段持久化到硬盘，当 Translog 的数据量达到512MB 或者 30 分钟时，会触发一次 Flush。
index.translog.flush_threshold_size 参数的默认值是 512MB，我们进行修改。
增加参数值意味着文件缓存系统中可能需要存储更多的数据，所以我们需要为操作系统的文件缓存系统留下足够的空间。
```

##### 减少副本的数量

```
ES 为了保证集群的可用性，提供了 Replicas（副本）支持，然而每个副本也会执行分析、索引及可能的合并过程，所以 Replicas 的数量会严重影响写索引的效率。
当写索引时，需要把写入的数据都同步到副本节点，副本节点越多，写索引的效率就越慢。
如 果 我 们 需 要 大 批 量 进 行 写 入 操 作 ， 可 以 先 禁 止 Replica 复 制 ， 设 置index.number_of_replicas: 0 关闭副本。在写入完成后，Replica 修改回正常的状态
```

#### 内存设置

```
ES 默认安装后设置的内存是 1GB，对于任何一个现实业务来说，这个设置都太小了。如果是通过解压安装的 ES，则在 ES 安装文件中包含一个 jvm.option 文件，添加如下命令来设置 ES 的堆大小，Xms 表示堆的初始大小，Xmx 表示可分配的最大内存，都是 1GB。
确保 Xmx 和 Xms 的大小是相同的，其目的是为了能够在 Java 垃圾回收机制清理完堆区后不需要重新分隔计算堆区的大小而浪费资源，可以减轻伸缩堆大小带来的压力。
假设你有一个 64G 内存的机器，按照正常思维思考，你可能会认为把 64G 内存都给ES 比较好，但现实是这样吗， 越大越好？虽然内存对 ES 来说是非常重要的，但是答案是否定的！
因为 ES 堆内存的分配需要满足以下两个原则：
 不要超过物理内存的 50%：Lucene 的设计目的是把底层 OS 里的数据缓存到内存中。Lucene 的段是分别存储到单个文件中的，这些文件都是不会变化的，所以很利于缓存，同时操作系统也会把这些段文件缓存起来，以便更快的访问。如果我们设置的堆内存过大，Lucene 可用的内存将会减少，就会严重影响降低 Lucene 的全文本查询性能。
 堆内存的大小最好不要超过 32GB：在 Java 中，所有对象都分配在堆上，然后有一个 Klass Pointer 指
针指向它的类元数据。
这个指针在 64 位的操作系统上为 64 位，64 位的操作系统可以使用更多的内存（2^64）。在 32 位的系统上为 32 位，32 位的操作系统的最大寻址空间为 4GB（2^32）。
但是 64 位的指针意味着更大的浪费，因为你的指针本身大了。浪费内存不算，更糟糕的是，更大的指针在主内存和缓存器（例如 LLC, L1 等）之间移动数据的时候，会占用更多的带宽。
最终我们都会采用 31 G 设置
假设你有个机器有 128 GB 的内存，你可以创建两个节点，每个节点内存分配不超过 32 GB。 也就是说不超过 64 GB 内存给 ES 的堆内存，剩下的超过 64 GB 的内存给 Lucene
```

####  重要配置

```
cluster.name elasticsearch 配置 ES 的集群名称，默认值是 ES，建议改成与所存数据相关的名称，ES 会自动发现在同一网段下的
集群名称相同的节点
node.name node-1 集群中的节点名，在同一个集群中不能重复。节点的名称一旦设置，就不能再改变了。当然，也可以设 置 成 服 务 器 的 主 机 名 称 ， 例 如node.name:${HOSTNAME}。
node.master true 指定该节点是否有资格被选举成为 Master 节点，默认是 True，如果被设置为 True，则只是有资格成为Master 节点，具体能否成为 Master 节点，需要通过选举产生。
node.data true 指定该节点是否存储索引数据，默认为 True。数据的增、删、改、查都是在 Data 节点完成的。
index.number_of_shards 1 设置都索引分片个数，默认是 1 片。也可以在创建索引时设置该值，具体设置为多大都值要根据数据量的大小来定。如果数据量不大，则设置成 1 时效率最高
index.number_of_replicas 1 设置默认的索引副本个数，默认为 1 个。副本数越多，集群的可用性越好，但是写索引时需要同步的数据越多。
transport.tcp.compress true 设置在节点间传输数据时是否压缩，默认为 False，不压缩
discovery.zen.minimum_master_nodes 1 设置在选举 Master 节点时需要参与的最少的候选主节点数，默认为 1。如果使用默认值，则当网络不稳定时有可能会出现脑裂。合理的数值为 (master_eligible_nodes/2)+1 ，其中master_eligible_nodes 表示集群中的候选主节点数
discovery.zen.ping.timeout 3s 设置在集群中自动发现其他节点时 Ping 连接的超时时间，默认为 3 秒。在较差的网络环境下需要设置得大一点，防止因误判该节点的存活状态而导致分片的转移

```

