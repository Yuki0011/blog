##  导入
### 模版下载
##### ***打成jar包的项目下载文件报错问题***
当项目打包成jar包后，不能够通过路径再去寻找文件要通过流来读取文件

```
String path = linuxOrWinPath(".xlsx");     //路径 FileUtils.downloadFileLocal(getClass().getClassLoader().getResourceAsStream("templates/excel/LeaceCost.xlsx"),path);  //将文件下载到本地

public static void downloadFileLocal(InputStream is, String destPath) throws IOException {
        // 万一路径没有呢
        mkdirsIfNotExists(destPath);
        OutputStream os = new FileOutputStream(destPath);
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(os);
        int length;
        byte[] buffer = new byte[2048];
        while ((length = is.read(buffer)) != -1) {
            bufferedOutputStream.write(buffer, 0, length);
        }
        bufferedOutputStream.flush();
        bufferedOutputStream.close();
        os.close();
        is.close();
    }
    
    
    public static void mkdirsIfNotExists(String path) {
        if (StringUtils.isEmpty(path)) {
            return;
        }
        File parentFile = new File(path).getParentFile();
        if (!parentFile.exists()) {
            parentFile.mkdirs();
        }
    }
    
public String linuxOrWinPath(String  type){
        String tmpdir = System.getProperty("java.io.tmpdir");
        String fileName = IdWorker.get32UUID() + type;
        String pdfdir = tmpdir;
        if (com.baomidou.kisso.common.OperatingSystem.get() == com.baomidou.kisso.common.OperatingSystem.OS.WINDOWS && pdfdir.endsWith("\\")) {
            pdfdir = pdfdir.substring(0, pdfdir.lastIndexOf("\\"));
        } else if (com.baomidou.kisso.common.OperatingSystem.get() == com.baomidou.kisso.common.OperatingSystem.OS.UNIX && !pdfdir.endsWith(File.separator)) {
            pdfdir += File.separator;
        }
        return  pdfdir+fileName;
    }
```

1.单个模版下载

```java
@GetMapping("/download")
public void downloadFile(HttpServletRequest request,
                             HttpServletResponse response) throws Exception {
        response.setContentType("text/html;charset=UTF-8");
        BufferedInputStream in = null;
        BufferedOutputStream out = null;
        request.setCharacterEncoding("UTF-8");
        try {
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("templates/模版.xlsx");
            response.setContentType("application/msexcel");
            response.setCharacterEncoding("UTF-8");
            response.setHeader("Content-Disposition", "attachment; filename=" + java.net.URLEncoder.encode("模版.xlsx", "UTF-8"));
            in = new BufferedInputStream(inputStream);
            out = new BufferedOutputStream(response.getOutputStream());
            byte[] data = new byte[1024];
            int len = 0;
            while (-1 != (len = in.read(data, 0, data.length))) {
                out.write(data, 0, len);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (in != null) {
                in.close();
            }
            if (out != null) {
                out.close();
            }
        }

    }
//下载不同的文件，ContentType不一样
HTML format (.htm or .html): text/html
Adobe Portable Document (pdf): application/pdf
Microsoft Word (.doc): application/msword
Microsoft Excel (.xls): application/msexcel
Microsoft Powerpoint (.ppt): application/ms-powerpoint
Realaudio (.rm, .ram): audio/x-pn-realaudio
Text format (.txt): text/txt
Zipped files (.zip): application/zip
```

2.多个模版下载，可以配置一个excel控制类

```java
@RestController
@RequestMapping("/excel")
public class ExcelController {

    @Resource
    public HttpServletResponse response;

    private static final Map<String, String> TPL = new HashMap<>();

    static {
        TPL.put("AllianceWarehouseInfoInput", "设备库导入模板");
        TPL.put("AllianceDeviceCleanInput", "清洁装置导入模板");
        TPL.put("AllianceDeviceElseInput", "其他装置导入模板");
    }


    @GetMapping("/template/{name}")
    public void template(@PathVariable("name") String name) {
        Assert.check(Objects.nonNull(this.TPL.get(name)), "导入模板不存请联系管理员");
        try {
            // 文件路径
            String filePath = String.format("templates/excel/input/%s.xlsx", name);   //格式化字符串
            // 兼容打包 JAR 压缩情况处理
            ExcelHelper.excelTemplateDownload(response, this.TPL.get(name), getClass().getClassLoader().getResourceAsStream(filePath));
        } catch (Throwable t) {
            Assert.error("该模板不存在");
        }
    }

}




/**
 * <p>
 * EXCEL 辅助类
 * </p>
 */
public class ExcelHelper {

    private static Logger logger = LoggerFactory.getLogger(ExcelHelper.class);

    /**
     * <p>
     * 请求下载 EXCEL 模板
     * </p>
     *
     * @param response     请求响应
     * @param templateName 模板名称
     * @param templateFile 模板文件
     */
    public static void excelTemplateDownload(HttpServletResponse response, String templateName, File templateFile) throws Exception {
        excelTemplateDownload(response, templateName, new FileInputStream(templateFile));
    }

    /**
     * <p>
     * 请求下载 EXCEL 模板
     * </p>
     *
     * @param response     请求响应
     * @param templateName 模板名称
     * @param in           输入流
     */
    public static void excelTemplateDownload(HttpServletResponse response, String templateName, InputStream in) {
        if (null != in || StringUtils.isNotEmpty(templateName)) {
            BufferedInputStream bis = null;
            BufferedOutputStream bos = null;
            try {
                setExcelHeader(response, templateName);
                ServletOutputStream out = response.getOutputStream();
                bis = new BufferedInputStream(in);
                bos = new BufferedOutputStream(out);
                byte[] buff = new byte[2048];
                int bytesRead;
                while (-1 != (bytesRead = bis.read(buff, 0, buff.length))) {
                    bos.write(buff, 0, bytesRead);
                }
            } catch (Exception e) {
                logger.error("下载模板失败：{}", e.getMessage());
            } finally {
                IoUtils.close(bis, bos, in);
            }
        }
    }

    /**
     * <p>
     * 设置 EXCEL 模板下载响应头
     * </p>
     *
     * @param response     请求响应
     * @param templateName 模板名称
     */
    public static void setExcelHeader(HttpServletResponse response, String templateName) throws UnsupportedEncodingException {
        // 设置response参数，可以打开下载页面
        response.reset();
        response.setContentType("application/vnd.ms-excel;charset=utf-8");
        // 下载文件的名称，中文转换
        response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(templateName, "UTF-8") + ".xlsx");
    }
}



/**
 * <p>
 * IO 工具类
 * </p>
 */
public class IoUtils {

    public static byte[] getBytesFromFile(String file) throws IOException {
        return getBytes(new FileInputStream(file));
    }

    public static byte[] getBytesFromResource(String resource) throws IOException {
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
        return getBytes(classLoader.getResourceAsStream(resource));
    }

    public static void writeFile(String file, byte[] bytes) throws IOException {
        FileOutputStream output = null;
        try {
            output = new FileOutputStream(file);
            output.write(bytes);
        } finally {
            IoUtils.close(output);
        }
    }

    public static byte[] getBytes(InputStream input) throws IOException {
        ByteArrayOutputStream output = null;
        try {
            output = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int length;
            while ((length = input.read(buffer)) >= 0) {
                output.write(buffer, 0, length);
            }
            return output.toByteArray();
        } finally {
            IoUtils.close(output, input);
        }
    }

    /**
     * <p>
     * 关闭流对象
     * </p>
     *
     * @param closeables 可关闭的流对象列表
     * @return IOException
     */
    public static IOException close(Closeable... closeables) {
        return close(Lists.newArrayList(closeables));
    }

    /**
     * <p>
     * 关闭流对象
     * </p>
     *
     * @param closeables 可关闭的流对象列表
     * @return IOException
     */
    public static IOException close(Collection<? extends Closeable> closeables) {
        if (CollectionUtils.isEmpty(closeables)) {
            return null;
        }
        IOException exception = null;
        for (Closeable closeable : closeables) {
            if (closeable != null) {
                try {
                    closeable.close();
                } catch (IOException e) {
                    // catch到之后继续保证后面没问题的一定会关掉
                    exception = e;
                }
            }
        }
        return exception;
    }





```
## 导入
第一种 代码量较少，对excel内容没有那么多校验 依赖easypoi
```java
public boolean importExcel(MultipartFile file, Account account ) {
        ImportParams importParams = new ImportParams();
        importParams.setTitleRows(0);
        importParams.setHeadRows(1);
        importParams.setSheetNum(1);
        List<TyphoonOilPlatform>typhoonOilPlatforms = new ArrayList<>();
        try {
            List<planformETO> planformETOS = ExcelImportUtil.importExcel(file.getInputStream(), planformETO.class, importParams);
            if (CollectionUtils.isEmpty(planformETOS)) {
                RestAssert.fail("无法找到数据,请检查导入模板");
            }
            for (int i = 0, z = planformETOS.size(); i < z; i++) {
                RestAssert.fail(StringUtils.isEmpty(planformETOS.get(i).getFieldName()), "第" + i + "行请填写油田名称");
                RestAssert.fail(StringUtils.isEmpty(planformETOS.get(i).getPlatformName()), "第" + i + "行请填写品平台名称");
            }
            Set<String> stringSet = planformETOS.stream().map(e -> e.getFieldName().trim()).collect(Collectors.toSet());
            List<TyphoonOilField> list = iTyphoonOilFieldService.list(Wrappers.<TyphoonOilField>query().select("id", "field_name").in("field_name", stringSet));
            RestAssert.fail(CollectionUtils.isEmpty(list), "您填写的油田名称"+stringSet+"不存在,只可以填写您系统中已存在的油田");
            Set<String>listNames = list.stream().map(e->e.getFieldName()).collect(Collectors.toSet());
            if (listNames.size() != stringSet.size()) {
                stringSet.removeAll(listNames);
                RestAssert.fail("您填写的" + stringSet + "并不存在于系统中,只能填写存在于系统中的名称,否则无法通过导入进行绑定!");
            }
            Map<String,Long>map = list.stream().collect(Collectors.toMap(e->e.getFieldName(),e->e.getId(),(e1, e2) -> e1));
            TyphoonOilPlatform typhoonOilPlatform;
            for (planformETO t :planformETOS){
                typhoonOilPlatform = new TyphoonOilPlatform();
                BeanUtils.copyProperties(t,typhoonOilPlatform);
               if (null !=t.getPlatformCordonOn() && Objects.equals(t.getPlatformCordonOn(),"否")){
                   typhoonOilPlatform.setPlatformCordonOn(0);
                }
               typhoonOilPlatform.setCreateName(account.getUserName());
                typhoonOilPlatform.setFieldIndex(map.get(t.getFieldName()));
                typhoonOilPlatforms.add(typhoonOilPlatform);
            }
        } catch (Exception e) {
            RestAssert.fail("导入文件失败!" + e.getMessage());
        }
        if(!CollectionUtils.isEmpty(typhoonOilPlatforms)){
            return saveBatch(typhoonOilPlatforms);
        }
       return false;
    }
```

       

