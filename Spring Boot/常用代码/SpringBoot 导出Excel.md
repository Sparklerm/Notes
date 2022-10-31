1、导入依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.1.1</version>
</dependency>
```

2、导出代码

```java
import com.alibaba.excel.EasyExcel;
import com.alibaba.excel.ExcelWriter;
import com.alibaba.excel.util.MapUtils;
import com.alibaba.excel.write.metadata.WriteSheet;
import com.alibaba.fastjson.JSON;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLEncoder;
import java.util.List;
import java.util.Map;

public static void WebExcelDownload(HttpServletResponse response, String fileName, Map<Class, List> sheetData) throws IOException {
    try {
        // 设置文件下载格式为 .xlsx
        response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
        // 设置编码为 UTF-8
        response.setCharacterEncoding("utf-8");
        // 设置下载头
        response.setHeader("Content-disposition", "attachment;filename*=utf-8''" + fileName + ".xlsx");
        // 设置Url FIleName编码，防止乱码
        fileName = URLEncoder.encode("测试", "UTF-8").replaceAll("\\+", "%20");

        // 写入Excel Data
        int sheetIndex = 0;
        ExcelWriter writer = EasyExcel.write(response.getOutputStream()).autoCloseStream(Boolean.FALSE).build();
        for (Class c : sheetData.keySet()) {
            if (sheetIndex == 0) {
                writer = EasyExcel.write(response.getOutputStream(), c).build();
                WriteSheet sheet = EasyExcel.writerSheet(sheetIndex++, c.getSimpleName()).build();
                writer.write(sheetData.get(c), sheet);
            } else {
                WriteSheet sheet = EasyExcel.writerSheet(sheetIndex++, c.getSimpleName()).head(c).build();
                writer.write(sheetData.get(c), sheet);
            }
        }
        writer.finish();
    } catch (Exception e) {
        // 重置response
        response.reset();
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        Map<String, String> map = MapUtils.newHashMap();
        map.put("status", "failure");
        map.put("message", "下载文件失败" + e.getMessage());
        response.getWriter().println(JSON.toJSONString(map));
    }
}
```

