---
title: java实现excel模板导出数据
copyright: true
date: 2018-07-06 17:14:58
tags: 
	- excel
	- java
	- 模板
categories: java
---
web项目导出excel有很多种方法，个人觉得使用`excel模板`导出比较好用，可以满足甲方对excel格式的多种需求，而且实现起来方便。

准备需要的jar：[下载地址](https://download.csdn.net/download/lvchengfengdehao/10524351)
freemarker-2.3.19.jar
freemarker-util-0.0.1.jar
jxl-2.6.10.jar
jxl-report-1.0.jar
<!-- more -->
maven项目`pom.xml`配置：
```
<!-- excel模板依赖start -->
<dependency>
  <groupId>org.freemarker</groupId>
  <artifactId>freemarker</artifactId>
  <version>2.3.19</version>
</dependency>
<dependency>
  <groupId>net.sourceforge.jexcelapi</groupId>
  <artifactId>jxl</artifactId>
  <version>2.6.10</version>
</dependency>
<dependency>
  <groupId>fakepath</groupId>
  <artifactId>freemarker-util</artifactId>
  <version>0.0.1</version>
</dependency>
<dependency>
  <groupId>fakepath</groupId>
  <artifactId>jxl-report</artifactId>
  <version>1.0</version>
</dependency>
<!-- excel模板依赖 end-->
```
java代码实现：
```
@RequestMapping("/exportExcel")
	@ResponseBody
	public Map<String,Object> exportExcel(HttpServletRequest request,@RequestBody Map<String, Object> map) throws Exception {
	    logger.info("------------开始执行下载任务-----------");
		Map<String,Object> result = new HashMap<String,Object>();
		result.put("result",true);
		result.put("msg","执行成功");
		try {
	    	String downloadPath="/export";//导出文件夹
			//查询导出数据
			Map<String,Object> resultMap = reportService.queryExportData(map);
			//目录生成
			ExcelUtil.mkdir(downloadPath);
			String filename = UUID.randomUUID().toString().replace("-", "").toUpperCase()+".xls";
			File f = new File(downloadPath+"/" + File.separatorChar + filename);
			// 模板生成Excel
			ReportEnginer enginer = new ReportEnginer();
			//模板存储路径
			String modelPath = request.getSession().getServletContext().getRealPath("/")+ "/template/model.xls";
			InputStream inputStream = new FileInputStream(new File(modelPath));
			OutputStream outputStream = new FileOutputStream(f);
			enginer.excute(inputStream, resultMap, outputStream);
			inputStream.close();
			outputStream.close();
			downloadDetail.setResult(filename);
			
		} catch (Exception e) {
			e.printStackTrace();
			result.put("result",false);
			result.put("msg","执行失败");
			logger.info("------------下载任务执行失败-----------");
		}
		logger.info("------------下载任务执行完成-----------");
		return result;
	}
```
代码中`resultMap`如下：
{datalist=[{hours=9, name=张三, cost=10}, {hours=32, name=李四, cost=6}]}
excle模板：使用etl表达式
![](https://i.imgur.com/W9TSAH6.png)
结果：
![](https://i.imgur.com/vNZjglR.png)