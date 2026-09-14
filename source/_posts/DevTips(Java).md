---
tags:
  - tips
categories:
  - dev
author: pairs
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

#### 7.接口接收附件，单个以及多个写法（参数使用MultipartFile[]不对，要使用`List<MultipartFile>`)

```java
// 多个附件
@Operation(summary = "上传文件")
@PostMapping(
    value = "/data-package/import",
    consumes = MediaType.MULTIPART_FORM_DATA_VALUE,
    name = "数据包批量导入处理")
public ResponseEntity<?> upload(
    @RequestParam(value = "fileList", required = false) List<MultipartFile> fileList) {
}

// 单个附件
@Operation(summary = "上传文件")
@PostMapping(
    value = "/data-package/import",
    consumes = MediaType.MULTIPART_FORM_DATA_VALUE,
    name = "数据包批量导入处理")
public ResponseEntity<?> upload(
    @RequestParam(value = "file", required = false) MultipartFile file) {
}
```

#### 8.通过List直接构建Map，对象的ID为键，对象本身为值

```java
// 存在一个List<Part> allPartsList
Map<String, Part> partMap = allPartsList.stream()
    .filter(part -> part.getMasterId() != null)
    .collect(Collectors.toMap(
        Part::getMasterId,
        Function.identity()
    ));
```

#### 9.Java生成指定大小svg二维码并且嵌入图片

下面的代码展示了从生成二维码到嵌入指定要是图片的过程，其中包括二维码嵌入后的缩放，这里是调试样式的重点。

```java
import com.casic.cpdm.enterprise.BusinessException;
import com.google.zxing.EncodeHintType;
import com.google.zxing.common.BitMatrix;
import com.google.zxing.qrcode.QRCodeWriter;
import com.google.zxing.qrcode.decoder.ErrorCorrectionLevel;
import java.util.HashMap;
import java.util.Map;
import org.apache.commons.lang3.StringUtils;
import org.springframework.stereotype.Component;

@Component
public class SVGQRCodeGenerator {

  /** 生成完整的SVG文档 */
  public String generateCompleteSVG(
      String spec,
      String qrCodeContent,
      String productName,
      String productionDate,
      String equipmentCode,
      String uniqueCode) {
    // 构建完整SVG 根据不同规格生成不同的完整SVG
    // 这里四种规格的二维码，16，34，16，25分别标识各个规格需要的二维码的实际边长（单位mm）
    if (StringUtils.equals(spec, "A1")) {
      Map<String,Object> codeMap = generateNoMarginQRCodeSVG(qrCodeContent,16);
      return buildFinalSVGA1(codeMap);
    } else if (StringUtils.equals(spec, "A2")) {
      Map<String,Object> codeMap = generateNoMarginQRCodeSVG(qrCodeContent,34);
      return buildFinalSVGA2(codeMap,uniqueCode);
    } else if (StringUtils.equals(spec, "A3")) {
      Map<String,Object> codeMap = generateNoMarginQRCodeSVG(qrCodeContent,16);
      return buildFinalSVGA3(codeMap, uniqueCode);
    } else if (StringUtils.equals(spec, "A4")) {
      Map<String,Object> codeMap = generateNoMarginQRCodeSVG(qrCodeContent,25);
      return buildFinalSVGA4(codeMap, productName, productionDate, equipmentCode, uniqueCode);
    } else {
      throw new BusinessException("规格不存在！");
    }
  }

  /**
   * 生成无白边SVG二维码（核心方法）
   */
  private Map<String,Object> generateNoMarginQRCodeSVG(String content,int sizeMm) {
    try {
      // 使用ZXing生成二维码
      QRCodeWriter qrCodeWriter = new QRCodeWriter();
      Map<EncodeHintType, Object> hints = new HashMap<>();
      hints.put(EncodeHintType.MARGIN, 0); // 关键：无白边
      hints.put(EncodeHintType.ERROR_CORRECTION,
          ErrorCorrectionLevel.M);
      // 改这里：改为0，使得二维码自适应模块
      BitMatrix bitMatrix = qrCodeWriter.encode(
          content,
          com.google.zxing.BarcodeFormat.QR_CODE,
          0,
          0,
          hints
      );
      // 转换为SVG
      String svgContent = bitMatrixToSVG(bitMatrix, sizeMm);
      int width = bitMatrix.getWidth();
      Map<String,Object> codeMap = new HashMap<>();
      codeMap.put("modules",width);
      codeMap.put("svgContent",svgContent);
      return codeMap;
    } catch (Exception e) {
      throw new RuntimeException("二维码生成失败！");
    }
  }

  /**
   * BitMatrix转SVG（无白边版本）
   */
  private String bitMatrixToSVG(BitMatrix matrix, int sizeMM) {
    int width = matrix.getWidth();  // 二维码模块数
    int height = matrix.getHeight();

    StringBuilder svg = new StringBuilder();
    svg.append("<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n");
    svg.append("<svg ")
        .append("width=\"").append(sizeMM).append("mm\" ")
        .append("height=\"").append(sizeMM).append("mm\" ")
        .append("viewBox=\"0 0 ").append(width).append(" ").append(height).append("\" ")
        .append("xmlns=\"http://www.w3.org/2000/svg\">\n");
    // 使用路径绘制二维码
    StringBuilder pathData = new StringBuilder();
    for (int y = 0; y < height; y++) {
      for (int x = 0; x < width; x++) {
        if (matrix.get(x, y)) {
          pathData.append("M").append(x).append(",").append(y)
              .append("h1v1h-1z ");
        }
      }
    }
    if (pathData.length() > 0) {
      svg.append("<path d=\"").append(pathData).append("\" fill=\"black\"/>\n");
    }
    svg.append("</svg>");
    return svg.toString();
  }

  /** 构建最终SVG文档（70mm x 40mm） */
  private String buildFinalSVGA4(
      Map<String, Object> codeMap,
      String productName,
      String productionDate,
      String equipmentCode,
      String uniqueCode) {
    String qrSvg = (String) codeMap.get("svgContent");
    int modules = (int) codeMap.get("modules");
    // 提取二维码SVG内容（去掉XML声明和根标签）
    String qrContent = extractQRContent(qrSvg);
    // 这里求缩放值用到的25就是实际生成二维码的边长，除以模块数量，则得到最终的缩放比例，就算两次二维码内容一次很多，一次很少，生成二维码级别不同，模块数量不     同，也能通过这种方式获得的缩放实现两次二维码最终大小一致。
    double scale = (double) 25 / modules;
    return "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"
        + "<svg width=\"70mm\" height=\"40mm\" viewBox=\"0 0 70 40\" xmlns=\"http://www.w3.org/2000/svg\">\n"
        + "  <!-- 全局样式 -->\n"
        + "  <style>\n"
        + "    .info-text { font-family: 'SimSun', '宋体', sans-serif; font-size: 2px; fill: #000; }\n"
        + "    .border { fill: none; stroke: #000; stroke-width: 0.2; }\n"
        + "  </style>\n"
        + "  \n"
        + "  <!-- 白色背景 -->\n"
        + "  <rect width=\"70\" height=\"40\" fill=\"white\" class=\"border \"/>\n"
        + "  \n"
        + "  <!-- 左侧信息区域 (35mm宽) -->\n"
        + "  <rect x=\"7.5\" y=\"7.5\" width=\"30\" height=\"25\" class=\"border\"/>\n"
        + "  \n"
        + "  <!-- 左侧信息内容 -->\n"
        + "  <g class=\"info-text\">\n"
        + "    <text x=\"10\" y=\"11\">产品名称："
        + escapeXml(productName)
        + "</text>\n"
        + "    <text x=\"10\" y=\"15\">生产日期："
        + escapeXml(productionDate)
        + "</text>\n"
        + "    <text x=\"10\" y=\"19\">装备品种码：</text>\n"
        + "    <text x=\"10\" y=\"22\">"
        + escapeXml(equipmentCode)
        + "</text>\n"
        + "    <text x=\"10\" y=\"26\">唯一标识码：</text>\n"
        + "    <text x=\"10\" y=\"29\">"
        + escapeXml(uniqueCode)
        + "</text>\n"
        + "  </g>\n"
        + "  \n"
        + "  <!-- 二维码（正方形，无边框，调整位置和大小） -->\n"
        + "  <g transform=\"translate(38, 7.5) scale(" + scale + ")\">\n"
        + // 改这里：调整位置和缩放
        "    "
        + qrContent
        + "\n"
        + "  </g>\n"
        + "</svg>";
  }

  /**
   * 构建最终SVG文档（50mm x 20mm）
   */
  private String buildFinalSVGA3(Map<String,Object> codeMap,String uniqueCode) {
    String qrSvg = (String) codeMap.get("svgContent");
    int modules = (int) codeMap.get("modules");
    // 提取二维码SVG内容（去掉XML声明和根标签）
    String qrContent = extractQRContent(qrSvg);
    double scale = (double) 16 / modules;
    return "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"
        + "<svg width=\"50mm\" height=\"20mm\" viewBox=\"0 0 50 20\" xmlns=\"http://www.w3.org/2000/svg\">\n"
        + "  <!-- 全局样式 -->\n"
        + "  <style>\n"
        + "    .info-text { font-family: 'SimSun', '宋体', sans-serif; font-size: 1.9px; fill: #000; }\n"
        + "    .border { fill: none; stroke: #000; stroke-width: 0.2; }\n"
        + "  </style>\n"
        + "  \n"
        + "  <!-- 白色背景 -->\n"
        + "  <rect width=\"50\" height=\"20\" fill=\"white\" class=\"border \"/>\n"
        + "  \n"
        + "  <!-- 左侧信息区域 (35mm宽) -->\n"
        + "  <rect x=\"2\" y=\"2\" width=\"29.5\" height=\"16\" class=\"border\"/>\n"
        + "  \n"
        + "  <!-- 左侧信息内容 -->\n"
        + "  <g class=\"info-text\">\n"
        + "    <text x=\"5\" y=\"10.5\">"
        + escapeXml(uniqueCode)
        + "</text>\n"
        + "  </g>\n"
        + "  \n"
        + "  <!-- 二维码（正方形，无边框，调整位置和大小） -->\n"
        + "  <g transform=\"translate(32, 2) scale(" + scale + ")\">\n"
        + // 改这里：调整位置和缩放
        "    "
        + qrContent
        + "\n"
        + "  </g>\n"
        + "</svg>";
  }

  /**
   * 构建最终SVG文档（50mm x 50mm）
   */
  private String buildFinalSVGA2(Map<String,Object> codeMap,String uniqueCode) {
    String qrSvg = (String) codeMap.get("svgContent");
    int modules = (int) codeMap.get("modules");
    // 提取二维码SVG内容（去掉XML声明和根标签）
    String qrContent = extractQRContent(qrSvg);
    double scale = (double) 34 / modules;
    return "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"
        + "<svg width=\"50mm\" height=\"50mm\" viewBox=\"0 0 50 50\" xmlns=\"http://www.w3.org/2000/svg\">\n"
        + "  <!-- 全局样式 -->\n"
        + "  <style>\n"
        + "    .info-text { font-family: 'SimSun', '宋体', sans-serif; font-size: 2.8px; fill: #000; }\n"
        + "    .border { fill: none; stroke: #000; stroke-width: 0.2; }\n"
        + "  </style>\n"
        + "  \n"
        + "  <!-- 白色背景 -->\n"
        + "  <rect width=\"50\" height=\"50\" fill=\"white\" class=\"border \"/>\n"
        + "  \n"
        + "  <!-- 二维码（正方形，无边框，调整位置和大小） -->\n"
        + "  <g transform=\"translate(8, 5) scale(" + scale + ")\">\n"
        + // 改这里：调整位置和缩放
        "    "
        + qrContent
        + "\n"
        + "  </g>\n"
        + "  <!-- 下面信息内容 -->\n"
        + "  <g class=\"info-text\">\n"
        + "    <text x=\"7.5\" y=\"42\">"
        + escapeXml(uniqueCode)
        + "</text>\n"
        + "  </g>\n"
        + "  \n"
        + "</svg>";
  }

  /**
   * 构建最终SVG文档（20mm x 20mm）
   */
  private String buildFinalSVGA1(Map<String,Object> codeMap) {
    String qrSvg = (String) codeMap.get("svgContent");
    int modules = (int) codeMap.get("modules");
    // 提取二维码SVG内容（去掉XML声明和根标签）
    String qrContent = extractQRContent(qrSvg);
    double scale = (double) 16 / modules;
    return "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"
        + "<svg width=\"20mm\" height=\"20mm\" viewBox=\"0 0 20 20\" xmlns=\"http://www.w3.org/2000/svg\">\n"
        + "  <!-- 全局样式 -->\n"
        + "  <style>\n"
        + "    .info-text { font-family: 'SimSun', '宋体', sans-serif; font-size: 2.8px; fill: #000; }\n"
        + "    .border { fill: none; stroke: #000; stroke-width: 0.2; }\n"
        + "  </style>\n"
        + "  \n"
        + "  <!-- 白色背景 -->\n"
        + "  <rect width=\"20\" height=\"20\" fill=\"white\" class=\"border \"/>\n"
        + "  \n"
        + "  <!-- 二维码（正方形，无边框，调整位置和大小） -->\n"
        + "  <g transform=\"translate(2, 2) scale(" + scale + ")\">\n"
        + // 改这里：调整位置和缩放
        "    "
        + qrContent
        + "\n"
        + "  </g>\n"
        + "</svg>";
  }

  /**
   * 提取二维码SVG内容
   */
  private String extractQRContent(String svg) {
    // 移除XML声明和根标签，只保留内部内容
    return svg.replace("<?xml version=\"1.0\" encoding=\"UTF-8\"?>", "")
        .replaceFirst("<svg[^>]*>", "")
        .replace("</svg>", "")
        .trim();
  }

  /**
   * XML特殊字符转义
   */
  private String escapeXml(String input) {
    if (input == null) return "";
    return input.replace("&", "&amp;")
        .replace("<", "&lt;")
        .replace(">", "&gt;")
        .replace("\"", "&quot;")
        .replace("'", "&#39;");
  }
}
```

#### 10.@ConditionalOnClass注解

```java
@Component
@ConditionalOnClass({RestHighLevelClient.class})
public class EspIndexUtils {
    ...
}
```

`@ConditionalOnClass({RestHighLevelClient.class})` 是 Spring Boot 中一个**条件化配置注解**，它的作用如下：

只用当classpath中存在RestHighLevelClient类时，才会加载被注解的配置类或Bean（EspIndexUtils）

classpath存在表示maven依赖中引用了该类的依赖

```xml
<!-- 如果 pom.xml 有这个依赖 -->
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.17.0</version>
</dependency>
```

#### 11.HashMap与LinkedHashMap

HashMap是无序的，你按照顺序遍历放进去的数据，最终查看HashMap的内容时，可能不是按照放入的顺序来展示的

LinkedHashMap是有序的，如果需要后续遍历Map内容按照放入顺序展示的时候使用LinkedHashMap


