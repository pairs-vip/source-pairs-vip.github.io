---
tags:
  - tips
categories:
  - dev
author: zmu
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
date: 2025-09-01 09:55:00
updated: 2025-09-01 09:55:00
title: DevTips(Java)
---

# DevTips(Java)

#### 1.`@RequestBody` 不能和文件参数（`MultipartFile`）同时使用。

 **原因分析**

+ **HTTP请求内容类型冲突** 
  + `@RequestBody` 期望 `Content-Type: application/json` 或类似类型，用于读取整个请求体作为单个对象` 
  + `文件上传使用 `Content-Type: multipart/form-data`，请求体被分成多个部分（parts）
+ **Spring MVC的处理机制** 

```http
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="test.dat"
Content-Type: application/octet-stream

[文件二进制内容]
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="hexStr"

额外的16进制字符串
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```



#### 2.Elasticsearch exception [type=cluster_block_exception, reason=blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];]"}

+ es存储内存不足报错，导致无法正常使用，紧急解决方案：

  在kibana的dev Tools控制台中输入命令执行：

```json
PUT _all/_settings
{
  "index.blocks.read_only_allow_delete": null
}
```

+ 查询es内存存储状态命令，kibana中输入指令执行：

```json
GET _cat/allocation?v
```



#### 3.org.apache.commons.lang3.StringUtils.isNotBlank()和isNotEmpty的区别

`StringUtils.isNotBlank` 和 `StringUtils.isNotEmpty` 都用于检查字符串是否“有效”，但它们的判断标准有**关键区别**。

简单来说，**`isNotBlank` 是 `isNotEmpty` 的“加强版”**，它更严格。

------

**核心区别**

| 方法                         | 关注点   | 检查内容                                                     | 严格程度 |
| :--------------------------- | :------- | :----------------------------------------------------------- | :------- |
| **`isNotEmpty(String str)`** | **长度** | 字符串**不为 `null`** 且**长度大于零** (`length() > 0`)      | 较宽松   |
| **`isNotBlank(String str)`** | **内容** | 字符串**不为 `null`**、**长度大于零**且**包含至少一个非空白字符** | 更严格   |

**`isNotBlank` 在 `isNotEmpty` 的基础上，额外排除了纯空白字符（如空格、制表符、换行符等）的情况。**



#### 4.分页对象实体需要进行转换的场景

**通过JPA框架查询回来的分页中对象是数据库中的对象类型，直接返回给前端不合适，所以需要将分页中对象进行转换成VO后返回给前端，并且不影响其他部分，例如分页信息(page,size)**

**直接使用分页对象.map遍历，然后执行转换方法即可。**

```java
Page<?>.map(entity -> {
    return convert(batchEntity)
})
```

```java
// controller
Page<DeliveryBatchEntity> deliveryBatchEntities = deliveryBatchService.pageBatches(form, PageRequest.of(page, size));
// 将分页content转换为DeliveryBatchVO类型展示
Page<DeliveryBatchVO> batchVOPage = deliveryBatchEntities.map(deliveryBatchConverter::convert);

//service调用JPA框架直接查询出分页
Page<DeliveryBatchEntity> deliveryBatchEntityPage = deliveryBatchRepository.findAll(Example.of(deliveryBatchEntityQuery,matcher), pageable);
```



#### 5.通过Example.of()进行模糊查询

通过这条语句配置那些字段进行模糊查询。

***matcher = matcher.withMatcher("batchName", ExampleMatcher.GenericPropertyMatchers.contains());***

```java
@Override
public Page<DeliveryBatchEntity> pageBatches(DeliveryBatchFormDTO form, Pageable pageable) {
    DeliveryBatchEntity deliveryBatchEntityQuery = new DeliveryBatchEntity();
    // 添加模糊查询
    ExampleMatcher matcher = ExampleMatcher.matching()
        .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING)
        .withIgnoreCase();
    matcher = matcher.withMatcher("batchName", ExampleMatcher.GenericPropertyMatchers.contains());
    // 封装查询对象
    deliveryBatchEntityQuery.setRepositoryId(form.getRepositoryId());
    deliveryBatchEntityQuery.setBatchType(form.getBatchType());
    if(StringUtils.isNotBlank(form.getBatchName())){
        deliveryBatchEntityQuery.setBatchName(form.getBatchName());
    }
    Page<DeliveryBatchEntity> deliveryBatchEntityPage = deliveryBatchRepository.findAll(
        Example.of(deliveryBatchEntityQuery,matcher), pageable);
    return  deliveryBatchEntityPage;
}
```



#### 6.JPA通过QuerySpec进行多表联查及条件筛选（Corilead版本，封装原版的QuerySpec）

```java
@Override
public Page<AbstractPart> getDesignPartByFolder(String folderId,DeliveryBomDTO query, Pageable pageable) {
    QuerySpec querySpec = new QuerySpec();
    // 添加第一个表 C_FOLDER_MEMBERS
    Integer folderMemberTableIndex = querySpec.appendTable(FolderMemberEntity.class.getName(), false);
    // 添加第二个表 C_DESIGN_PART
    Integer designPartTableIndex = querySpec.appendTable(DesignPart.class.getName(), true);
    // 添加第三个表 C_DESIGN_PART_MASTER
    Integer designPartMasterTableIndex = querySpec.appendTable(DesignPartMaster.class.getName(), false);
    // 构建关联条件：FOLDER_MEMBER.ObjectId = DESIGN_PART.MASTER_ID
    SearchCondition joinCondition1 = new SearchCondition(
        "objectId",
        SearchCondition.EQUAL,
        "masterId",
        new Integer[] {folderMemberTableIndex, designPartTableIndex}
    );
    // 构建关联条件：FOLDER_MEMBER.ObjectType = DESIGN_PART.MASTER_TYPE
    SearchCondition joinCondition2 = new SearchCondition(
        "objectType",
        SearchCondition.EQUAL,
        "masterType",
        new Integer[] {folderMemberTableIndex, designPartTableIndex}
    );
    // 构建关联条件：C_DESIGN_PART.masterId = C_DESIGN_PART_MASTER.id
    SearchCondition joinCondition3 = new SearchCondition(
        "masterId",
        SearchCondition.EQUAL,
        "id",
        new Integer[] {designPartTableIndex, designPartMasterTableIndex}
    );
    // 合并关联条件
    // 使用 AND 连接关联条件
    querySpec.appendWhere(joinCondition1);
    querySpec.appendWhere(SearchCondition.AND);
    querySpec.appendWhere(joinCondition2);
    querySpec.appendWhere(SearchCondition.AND);
    querySpec.appendWhere(joinCondition3);
    querySpec.appendWhere(SearchCondition.AND);
    // 添加条件：FOLDER_MEMBER.FOLDER_ID = 传入参数 folderId
    if (folderId != null) {
        SearchCondition folderCondition = new SearchCondition(
            "folderId",
            SearchCondition.EQUAL,
            new AttributeValue(folderId),
            new Integer[] {folderMemberTableIndex}
        );
        querySpec.appendWhere(folderCondition);
        querySpec.appendWhere(SearchCondition.AND);
    }
    // 获取最新版本
    SearchCondition searchCondition = new SearchCondition(
        "latestIteration",
        SearchCondition.EQUAL,
        new AttributeValue(true),
        new Integer[] {designPartTableIndex}
    );
    querySpec.appendWhere(searchCondition);
    // 添加查询条件编号
    if (StringUtils.isNotBlank(query.getCode())){
        querySpec.appendWhere(SearchCondition.AND);
        SearchCondition searchConditionCode = new SearchCondition(
            "code",
            SearchCondition.LIKE,
            new AttributeValue("%"+ query.getCode() +"%"),
            new Integer[] {designPartMasterTableIndex}
        );
        querySpec.appendWhere(searchConditionCode);
    }
    // 添加查询条件产品名称
    if (StringUtils.isNotBlank(query.getProductName())){
        querySpec.appendWhere(SearchCondition.AND);
        SearchCondition searchConditionProductName = new SearchCondition(
            "name",
            SearchCondition.LIKE,
            new AttributeValue("%"+ query.getProductName() +"%"),
            new Integer[] {designPartMasterTableIndex}
        );
        querySpec.appendWhere(searchConditionProductName);
    }
    // 添加查询条件状态
    if (StringUtils.isNotBlank(query.getState())){
        querySpec.appendWhere(SearchCondition.AND);
        SearchCondition searchConditionState = new SearchCondition(
            "stateId",
            SearchCondition.EQUAL,
            new AttributeValue(Long.valueOf(query.getState())),
            new Integer[] {designPartTableIndex}
        );
        querySpec.appendWhere(searchConditionState);
    }
    // 排序
    Sort sort = pageable.getSort();
    if (Sort.unsorted().equals(sort)) {
        querySpec.appendOrderBy(new OrderBy("modifyStamp", "desc", designPartTableIndex));
    } else {
        for (Sort.Order order : sort) {
            querySpec.appendOrderBy(
                new OrderBy(order.getProperty(), (order.isAscending()) ? "asc" : "desc", designPartTableIndex));
        }
    }
    Page<AbstractPart> abstractParts = persistenceService.find(querySpec, pageable);
    return abstractParts;
}
```

