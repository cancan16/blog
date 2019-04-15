---
title: Java
date: 2016-10-18 21:43:06
update: 2018-06-18 21:43:06
categories: WORK
tags: [java, 正则替换, enum]
---

### 1. 生成pdf文件
导包：
- D:\java-tools\apache-maven-3.2.3\repo\com\itextpdf\itextpdf\5.4.3\itextpdf-5.4.3.jar
- D:\java-tools\apache-maven-3.2.3\repo\com\itextpdf\itext-asian\5.2.0\itext-asian-5.2.0.jar

ReviewInvocation.java
<!-- more -->
```java

@Component("INVO_review")
public class ReviewInvocation implements BaseInvocation {
    private final Logger logger = LoggerFactory.getLogger(ReviewInvocation.class);

    @Autowired
    private IReviewService reviewService;

    public InvocationResult queryReviewList(InvocationContext context) {
	long begin = System.currentTimeMillis();
	InvocationResult result = InvocationResult.newInstance();
	String userNameSearch = RequestUtil.getStrParamterAsDef(context.getRequest(), "userNameSearch", "");
	String reviewTimeSearch = RequestUtil.getStrParamterAsDef(context.getRequest(), "reviewTimeSearch", "");
	String fUserMobile = RequestUtil.getStrParamterAsDef(context.getRequest(), "fUserMobile", "");
	int page = RequestUtil.getIntParamterAsDef(context.getRequest(), "page", 1);
	int rows = RequestUtil.getIntParamterAsDef(context.getRequest(), "rows", 0);
	try {
	    ReviewBean queryBean = new ReviewBean();
	    queryBean.setReviewTime(reviewTimeSearch);
	    queryBean.setUserName(userNameSearch);
	    queryBean.setfUserMobile(fUserMobile);
	    Map<String, Object> queryMap = reviewService.queryReviewList(queryBean, page, rows);
	    result.setRetObj(queryMap);
	    result.setA(ISystemConstants.SYS_SUCCESS);
	} catch (Exception ex) {
	    logger.error("ReviewInvocation queryReviewList happen execption.", ex);
	    result.setRetCode(ISystemConstants.SYS_FAILED);
	} finally {
	    long end = System.currentTimeMillis();
	    logger.info("ReviewInvocation queryReviewList cost " + (end - begin) + " ms!");
	}
	return result;
    }
    public InvocationResult queryReviewById(InvocationContext context) {
	long begin = System.currentTimeMillis();
	InvocationResult result = InvocationResult.newInstance();
	String reviewId = RequestUtil.getStrParamterAsDef(context.getRequest(), "reviewId", "");
	try {
	    ReviewBean reviewBean = reviewService.queryReviewById(reviewId);
	    result.setRetObj(reviewBean);
	    result.setA(ISystemConstants.SYS_SUCCESS);
	} catch (Exception ex) {
	    logger.error("ReviewInvocation queryReviewList happen execption.", ex);
	    result.setRetCode(ISystemConstants.SYS_FAILED);
	} finally {
	    long end = System.currentTimeMillis();
	    logger.info("ReviewInvocation queryReviewList cost " + (end - begin) + " ms!");
	}
	return result;
    }

    public InvocationResult updateReview(InvocationContext context) throws Exception {
	long begin = System.currentTimeMillis();
	InvocationResult result = InvocationResult.newInstance();
	String reviewId = RequestUtil.getStrParamterAsDef(context.getRequest(), "reviewId", "");
	String desc = RequestUtil.getStrParamterAsDef(context.getRequest(), "desc", "");
	String combo = RequestUtil.getStrParamterAsDef(context.getRequest(), "combo", "");
	String handleType = RequestUtil.getStrParamterAsDef(context.getRequest(), "handleType", "");
	String userId = RequestUtil.getStrParamterAsDef(context.getRequest(), "userId", "");
	ReviewBean reviewBean2 = new ReviewBean();
	String contractContent = "";
	if ("1".equals(handleType)) {
	    // 批量替换异花括号(包含)中的内容，'{name}'为'zhangsan'
	    HashMap<String, Object> param = new HashMap<String, Object>();
	    Map<String, String> tokens = new HashMap<String, String>();
	    param.put("fContractId", "1");
	    param.put("fContractType", "1");
	    ReviewBean reviewBeanTemp = reviewService.queryContractContent(param);
	    contractContent = reviewBeanTemp.getfContractContent();
	    ReviewBean reviewBean = reviewService.queryReviewById(reviewId);
	    String userName = reviewBean.getUserName();
	    String idNo = reviewBean.getIdNo();
	    String provinceName = reviewBean.getProvinceName();
	    String city = reviewBean.getCityName();
	    String areaName = reviewBean.getAreaName();
	    if (userName != null && userName != "") {
		tokens.put("name", userName);
	    }
	    if (idNo != null && idNo != "") {
		tokens.put("id", idNo);
	    }
	    if (provinceName != null && provinceName != "") {
		tokens.put("province", provinceName);
	    }
	    if (city != null && city != "") {
		city = city.substring(0, city.length() - 1);
		tokens.put("city", city);
	    }
	    if (areaName != null && areaName != "") {
		tokens.put("area", areaName);
	    }
	    String todayYear = DateTimeUtil.getTodayYear();
	    String todayMonth = DateTimeUtil.getTodayMonth();
	    String todayDay = DateTimeUtil.getTodayDay();
	    tokens.put("year", todayYear);
	    tokens.put("mon", todayMonth);
	    tokens.put("day", todayDay);
	    tokens.put("nyear", Integer.parseInt(todayYear) + 1 + "");
	    tokens.put("nmon", todayMonth);
	    tokens.put("nday", todayDay);
	    String patternString = "\\{(" + StringUtils.join(tokens.keySet(), "|") + ")\\}";
	    Pattern pattern = Pattern.compile(patternString);
	    Matcher matcher = pattern.matcher(contractContent);
	    StringBuffer sb = new StringBuffer();
	    while (matcher.find()) {
		matcher.appendReplacement(sb, tokens.get(matcher.group(1)));
	    }
	    matcher.appendTail(sb);
	    // 替换后协议内容
	    contractContent = sb.toString();
	}
	try {
	    if ("1".equals(handleType)) {
		SystemUserInfoBean userInfo = (SystemUserInfoBean) SessionUtil.getObjectAttribute(context.getRequest(),
			RequestConstants.ADMIN_SESSION_KEY);
		reviewService.updateReview(userId, reviewId, desc, combo, handleType,
			userInfo != null && userInfo.getfUserName() != null && !"".equals(userInfo.getfUserName())
				? userInfo.getfUserName() : "system");
		ReviewBean reviewBeanById = reviewService.queryReviewById(reviewId);
		String state = reviewBeanById.getState();
		String fStationId = reviewBeanById.getfStationId();
		if ("1".equals(state)) {
		    getPdf(contractContent);
		    String pdfName = addPic();
		    Map<String, Object> param = new HashMap<String, Object>();
		    // pdf访问路径
		    String fContractUrl = BaseWebProperties.PDF_READ + File.separator + pdfName + ".pdf";
		    param.put("fStationId", fStationId);
		    param.put("fContractUrl", fContractUrl);
		    reviewService.addContractInfo(param);
		}
		reviewBean2.setfContractContent(contractContent);
		result.setD(reviewBean2);
		result.setA(ISystemConstants.SYS_SUCCESS);
	    }
	} catch (Exception ex) {
	    logger.error("ReviewInvocation updateReview happen execption.", ex);
	    result.setRetCode(ISystemConstants.SYS_FAILED);
	} finally {
	    long end = System.currentTimeMillis();
	    logger.info("ReviewInvocation updateReview cost " + (end - begin) + " ms!");
	}
	return result;
    }

    // 创建
    public void getPdf(String contractContent) throws IOException {
	Document document = new Document(PageSize.A4, 50, 50, 50, 50);
	String pdfN = "cachePdf";
	File file = new File(BaseWebProperties.UPLOAD_PDF_PATH + File.separator + pdfN + ".pdf");
	try {
	    PdfWriter.getInstance(document, new FileOutputStream(file));
	    document.open();
	    BaseFont bf = BaseFont.createFont("STSong-Light", "UniGB-UCS2-H", BaseFont.NOT_EMBEDDED);
	    Font font = new Font(bf, 12, Font.NORMAL);
	    Image png = Image.getInstance(BaseWebProperties.PIC_READ + File.separator + "title.png");
	    png.scaleAbsolute(540, 70);
	    png.setAlignment(Image.ALIGN_CENTER);
	    document.add(png);
	    document.add(new Paragraph(contractContent, font));
	    document.close();
	} catch (FileNotFoundException e) {
	    e.printStackTrace();
	} catch (DocumentException e) {
	    e.printStackTrace();
	}
    }

    // 追加图片到pdf
    public String addPic() throws Exception {
	PdfReader reader = new PdfReader(BaseWebProperties.PDF_READ + File.separator + "cachePdf.pdf");
	int n = reader.getNumberOfPages();
	Document document = new Document(reader.getPageSize(n));
	float width = document.getPageSize().getWidth();
	String pdfName = DateTimeUtil.getTodayChar14();
	File file = new File(BaseWebProperties.UPLOAD_PDF_PATH + File.separator + pdfName + ".pdf");
	PdfStamper stamp = new PdfStamper(reader, new FileOutputStream(file));
	PdfContentByte over;
	Image img = Image.getInstance(BaseWebProperties.PIC_READ + File.separator + "sign1.PNG");
	img.scaleAbsolute(110, 110);
	img.setAbsolutePosition(width - 150, 350);
	img.setAlignment(Image.ALIGN_RIGHT);
	if (n > 0) {
	    over = stamp.getOverContent(n);
	    over.addImage(img);
	}
	stamp.close();
	File file2 = new File(BaseWebProperties.UPLOAD_PDF_PATH + File.separator + "cachePdf.pdf");
	if (file2.exists()) {
	    file2.delete();
	}
	return pdfName;
    }

}
```
* 正则表达式参考资源：http://jiangzhengjun.iteye.com/blog/513929
### 2. Map排序

```java
public static void main(String[] args) {
    Student student2 = new Student("b", "2");
    Student student3 = new Student("c", "3");
    Student student1 = new Student("a", "1");
    TreeMap<Integer, Object> treeMap = new TreeMap<Integer, Object>();//true
    treeMap.put(new Integer(2), student2);
    treeMap.put(new Integer(3), student3);
    treeMap.put(new Integer(1), student1);
    /**
     *
    Set<String> keySet = map.keySet();
    for(String key : keySet){
    	System.out.println("key:" + key + " | value:" + map.get(key));
    }
     */
    HashMap<Integer, Object> hashMap = new HashMap<Integer, Object>();
    hashMap.put(new Integer(2), student2);
    hashMap.put(new Integer(3), student3);
    hashMap.put(new Integer(1), student1);
    /**
    Iterator<Object> iterator = map.values().iterator();//排序
    while(iterator.hasNext()){
    	int value = (Integer)iterator.next();
    	System.out.println(value);
    }
     */
    LinkedHashMap<Integer, Object> likedMap = new LinkedHashMap<Integer, Object>();//不排序
    likedMap.put(new Integer(2), student2);
    likedMap.put(new Integer(3), student3);
    likedMap.put(new Integer(1), student1);
    /**
    Set<String> keySet2 = map.keySet();
    for(String key : keySet2){
    	System.out.println("key:" + key + " | value:" + map.get(key));
    }
     */
    System.out.println("down");

}

```
### 3. JAVA代理
#### 3.1 简单代理
Subject.java接口
```java
package com.bmx.omp.business.invocation.system;

public interface Subject {
    void operation1();

    void operation2(String arg);
}

```

RealSubject.java实现类。被代理对象
```java
package com.bmx.omp.business.invocation.system;

public class RealSubject implements Subject {//被代理对象

    public void operation1() {
       System.out.println("Realer do operation1");
    }

    public void operation2(String arg) {
       System.out.println("Realer do operation2 with " + arg);
    }
}

```
ProxySubject.java代理对象

```java
package com.bmx.omp.business.invocation.system;

public class ProxySubject implements Subject {
    private Subject proxied;// 被代理对象

    public ProxySubject(Subject proxied) {
       this.proxied = proxied;
    }

    public void operation1() {
       System.out.println("Proxyer do operation1");
       proxied.operation1();
    }

    public void operation2(String arg) {
       System.out.println("Proxyer do operation2 with " + arg);
       proxied.operation2(arg);
    }
}
```

测试类SimpleProxyDemo.java

```java
package com.zj.proxy.client;

import com.zj.proxy.Subject;
import com.zj.proxy.RealSubject;
import com.zj.proxy.ProxySubject;

public class SimpleProxyDemo {
    public static void consumer(Subject subject) {
        subject.operation1();
        subject.operation2("ZJ");
    }

    public static void main(String[] args) {
        RealSubject real = new RealSubject();
        System.out.println("===Without Proxy===");
        consumer(real);
        System.out.println("===Use Proxy===");
        consumer(new ProxySubject(real));
    }
}
```

结果
```java
结果：
===Without Proxy===
Realer do operation1
Realer do operation2 with ZJ
===Use Proxy===
Proxyer do operation1
Realer do operation1
Proxyer do operation2 with ZJ
Realer do operation2 with ZJ
```
#### 3.2 动态代理
DynamicProxyHandler.java代理处理类

```java
package com.omp.business.invocation.system;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

/**
 * @ClassName DynamicProxyHandler
 * @Description 动态代理处理类
 * @Author youtanzhi
 * @Date 2016年8月23日 下午7:01:55
 */
public class DynamicProxyHandler implements InvocationHandler {// 动态代理处理类
  private Object proxied;

  public DynamicProxyHandler(Object RealSubject) {
    this.proxied = RealSubject;//生成代理对象
  }

  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    System.out.println("**** proxy: ****\n" + proxy.getClass() + "\nmethod: " + method + "\nargs: " + args);
    if (args != null) {
      for (Object arg : args) {
        System.out.println("  " + arg);
      }
    }
    return method.invoke(proxied, args);
  }
}
```
动态代理测试类
```java
package com.omp.business.invocation.system;

import java.lang.reflect.Proxy;

/**
 * @ClassName DynamicProxyDemo
 * @Description 动态代理测试类
 * @Author youtanzhi
 * @Date 2016年8月23日 下午7:24:19
 */
public class DynamicProxyDemo {
    public static void consumer(Subject subject) {
        subject.operation1();//执行被代理对象调用的方法时，先执行代理对象的invoke方法
        subject.operation2("ZJ");
    }

    public static void main(String[] args) {
        RealSubject real = new RealSubject();
        System.out.println("===Without Proxy===");
        consumer(real);
        System.out.println("===Use Proxy===");
        Subject proxy = (Subject) Proxy.newProxyInstance(//三个参数
                            Subject.class.getClassLoader(), // 被代理对象类加载器
                            new Class[] {Subject.class}, // 代理类要实现的接口字节码列表(数组)
                            new DynamicProxyHandler(real));// 指派方法调用的处理程序
        consumer(proxy);// 先处理程序中的invoke方法载执行代理对象执行要执行的方法
    }
}

```
### 4. 请求头转换成map

```java
SortedMap<STRING, STRING> paramMap = new TreeMap<STRING, STRING>();
 Enumeration<STRING> em = request.getParameterNames();
 while
 (em.hasMoreElements()) {
     String paramKey = em.nextElement();
     paramMap.put(paramKey, request.getParameter(paramKey));
 }
 ```
### 5. list元素去重数组与list转换

```java
public class Com {
    public static void main(String[] args) {
	// 如何去重list集合中的元素
	// 方式一：使用for循环
	List<String> arr = new ArrayList<String>(); // aec
	arr.add("a");
	arr.add("b");
	arr.add("e");
	arr.add("e");
	arr.add("c");
	arr.add("e");
	arr.add("a");
	arr.add("b");
	arr.add("b");
//	for (int i = 0; i < arr.size(); i++) {
//	    for (int j = i + 1; j < arr.size(); j++) {
//		if (arr.get(i).equals(arr.get(j))) {
//		    arr.remove(j);
//		    if (j != arr.size()) {
//			j--;
//		    }
//		}
//	    }
//	}
//	for (int i = 0; i < arr.size(); i++) {
//	    System.out.println(arr.get(i));
//	}
	// 方式二
//	for (int i = 0; i < arr.size() - 1; i++) {
//	    for (int j = arr.size() - 1; j > i; j--) {
//		if (arr.get(j).equals(arr.get(i))) {
//		    arr.remove(j);
//		}
//	    }
//	}
//	System.out.println(arr);
	// 方式三 放到hashset中
//	Set h = new HashSet(arr);
//	arr.clear();
//	arr.addAll(h);
//	System.out.println(arr);
	// String类型list集合转String类型的数组
	String[] arrString = (String[]) arr.toArray(new String[] {});
	// String数组转String类型的list集合
	List<String> list = Arrays.asList(arrString);
	// 快速打印出数组中元素
	System.out.println(Arrays.toString(arrString));
	// int数组转list集合
	Integer[] intA = new Integer[] { 0, 1, 2 };
	List dataList = Arrays.asList(intA); // 说明了asList()d的参数要的是对象数组，而不是基本类型的数组
	System.out.println(dataList);

    }
}

```
### 6. SortedMap正序排序

```java
    public static void main(String[] args) {
	// SoredMap是正序排序
	SortedMap<String, Object> s = new TreeMap<String, Object>();
	// Map<String, Object> s = new HashMap<String, Object>();
	s.put("b", 2);
	s.put("c", 3);
	s.put("a", 1);
	Set<Entry<String, Object>> entrySet = s.entrySet();
	for (Entry i : entrySet) {
	    System.out.println("key :" + i.getKey());
	    System.out.println("value :" + i.getValue());
	}
    }
    /**
     * 结果： 
     * key :a value :1 key :b value :2 key :c value :3
     */
```
### 7. HashMap可放入TreeMap中进行排序
```java
	Map<String, Object> m = new HashMap<String, Object>();
	m.put("b", 2);
	m.put("c", 3);
	m.put("a", 1);
	SortedMap<String, Object> sort = new TreeMap<String, Object>(m);
	Set<Entry<String, Object>> entrySet = sort.entrySet();
	for (Entry e : entrySet) {
	    System.out.println(e.getKey());
	    System.out.println(e.getValue());
	}
```




### 8. ajax返回结果时机

```java
Object retObj = actionDispatcher.dispatchRequest(request, response, null);
 String s = JSONObject.toJSONString(retObj);
 PrintWriter pw = response.getWriter();
 pw.print(s); // 设置返回结果对象，并不返回
 pw.close();  // (这一步结束之后)PrintWriter对象关闭之后才返回对象，执行ajax的success方法
}

```

### 9. 请求体以utf8格式转码获取

```java
public static String getHttpBodyString(HttpServletRequest req) throws Exception
{
	
	InputStream in = req.getInputStream();
	byte[] data = new byte[1024];
	int readLen = 0;
	ByteArrayOutputStream bos = new ByteArrayOutputStream();
	while ((readLen = in.read(data)) != -1)
	{
		bos.write(data, 0, readLen);
	}
	return bos.toString("UTF-8");
}
```

### 10. 报错
Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is java.sql.SQLException: Timed out waiting for a free available connection.原因有可能是：

You call DB.getConnection() twice. Perhaps because of this the JDBC driver creates 2 connections, but you close only the second one.
	创建了两个连接，程序只关闭了第二个
	mybatis会自动关闭数据库连接

### 11. java调用声音

```java
import java.io.FileInputStream;
import sun.audio.AudioPlayer;
import sun.audio.AudioStream;

public class Sounda {
    public static void main(String args[]) {
	try {
	    FileInputStream fileau = new FileInputStream("E:/tet/1.wav");
	    AudioStream as = new AudioStream(fileau);
	    AudioPlayer.player.start(as);
	} catch (Exception e) {
	}
    }
}
```

### 12. 数值格式化

```java
// #.000000 保留六位小数，小数后不够六位会零填充 0.1 -> 0.100000
// #.###### 保留六位小数，小数后不够六位会把后面的零去掉 0.100 -> 0.1
DecimalFormat df = new DecimalFormat("#.000000");
String value = String.valueOf(df.format(inputValue));
```


### 枚举 enum
#### 普通枚举

```
package com.biusoft.core.modules.zhxc.utils;

/**
 * 缓存字段管理
 */
public enum ECacheFields {
    // 赔补偿标准和轻微违法
    COMPENSATION("COMPENSATION", 0);
    private String name;
    private int index;

    private ECacheFields(String name, int index) {
        this.name = name;
        this.index = index;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }

}

for (ECacheFields eCacheFields : ECacheFields.values()) {
	System.out.println("名称：" + eCacheFields.getName() + " 角标：" + eCacheFields.getIndex());
}
```
#### 接口枚举使用
```java
import com.baomidou.framework.common.IEnum;

/**
 * @Author: volc
 * @Description: 常量管理
 * @Date: 15:13 2018/1/25
 */
public interface Plant {

    /**
     * websocket消息类型
     */
    enum MessageType implements IEnum {

        // 推送消息
        PUSHMSG(0, "PUSHMSG"),
        // android发送给websocket服务的心跳包，用于反馈web端巡查车辆实时状态
        PATROL(1, "PATROL"),
        // web端获取车辆心跳包信息
        GETPATROL(2, "GETPATROL"),
        // 系统成功接收到消息后，消息回调更新消息状态
        MSGSTATUS(3, "MSGSTATUS");
        private final int key;
        private final String desc;

        MessageType(final int key, final String desc) {
            this.key = key;
            this.desc = desc;
        }

        @Override
        public int key() {
            return this.key;
        }

        @Override
        public String desc() {
            return this.desc;
        }

    }
}
```

### 递归遍历树结构list

```java 
/**
 * 递归查找子节点
 */
public static List<ProjectTree> findChildren(List<ProjectTree> projectTreeArrayList, String projectName, CompensationTree compensationTree) {
	if (!(compensationTree.getChildList().size() > 0)) {
		ProjectTree projectTree = new ProjectTree();
		projectTree.setProjName(projectName);
		projectTree.setId(compensationTree.getId()); // 数据id
		projectTree.setParentId(compensationTree.getParentId()); // 父事项id
		projectTree.setParentDataId(compensationTree.getParentDataId()); // 父事项id
		projectTree.setEventId(compensationTree.getEventId()); // 事项id
		projectTreeArrayList.add(projectTree);
		return projectTreeArrayList;
	} else if ((compensationTree.getChildList().size() > 0)) {
		for (Compensation compensation : compensationTree.getChildList()) {
			if (compensation.getChildList().size() > 0) {
				compensationTree.setChildList(compensation.getChildList());
				compensationTree.setEventId(compensation.getEventId());
				compensationTree.setParentId(compensation.getEventId());
				compensationTree.setId(compensation.getId());
				compensationTree.setParentDataId(compensation.getParentDataId());
				findChildren(projectTreeArrayList, projectName + "->" + compensation.getEventName(), compensationTree);
			} else if (!(compensation.getChildList().size() > 0)) {
				ProjectTree projectTree = new ProjectTree();
				projectTree.setProjName(projectName + "->" + compensation.getEventName());
				projectTree.setId(compensation.getId()); // 数据id
				projectTree.setParentDataId(compensation.getParentDataId());
				projectTree.setEventId(compensation.getEventId());
				projectTree.setParentId(compensation.getParentId());
				projectTreeArrayList.add(projectTree);
			}
		}
		return projectTreeArrayList;
	}
	return projectTreeArrayList;
}

```

### 遍历树结构

树结构实体

```java
import com.biusoft.core.common.persistence.DataEntity;
import java.util.ArrayList;
import java.util.List;
public class CompensationTree extends DataEntity<CompensationTree> {
    private String id;

    private String parentDataId;
    private String eventId;

    private String parentId;

    private String eventName;

    private String price;

    private String unit;

    private String isDel;

    private String sortNum;

    private String describe;

    private String type;

    private List<Compensation> childList = new ArrayList<>();

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id == null ? null : id.trim();
    }

    public String getEventId() {
        return eventId;
    }

    public void setEventId(String eventId) {
        this.eventId = eventId == null ? null : eventId.trim();
    }

    public String getParentId() {
        return parentId;
    }

    public void setParentId(String parentId) {
        this.parentId = parentId == null ? null : parentId.trim();
    }

    public String getEventName() {
        return eventName;
    }

    public void setEventName(String eventName) {
        this.eventName = eventName == null ? null : eventName.trim();
    }

    public String getPrice() {
        return price;
    }

    public void setPrice(String price) {
        this.price = price == null ? null : price.trim();
    }

    public String getUnit() {
        return unit;
    }

    public void setUnit(String unit) {
        this.unit = unit == null ? null : unit.trim();
    }

    public String getIsDel() {
        return isDel;
    }

    public void setIsDel(String isDel) {
        this.isDel = isDel == null ? null : isDel.trim();
    }

    public String getSortNum() {
        return sortNum;
    }

    public void setSortNum(String sortNum) {
        this.sortNum = sortNum == null ? null : sortNum.trim();
    }

    public String getDescribe() {
        return describe;
    }

    public void setDescribe(String describe) {
        this.describe = describe == null ? null : describe.trim();
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type == null ? null : type.trim();
    }

    public List<Compensation> getChildList() {
        return childList;
    }

    public void setChildList(List<Compensation> childList) {
        this.childList = childList;
    }

    public String getParentDataId() {
        return parentDataId;
    }

    public void setParentDataId(String parentDataId) {
        this.parentDataId = parentDataId;
    }

    @Override
    public String toString() {
        return "CompensationTree{" +
                "id='" + id + '\'' +
                ", parentDataId='" + parentDataId + '\'' +
                ", eventId='" + eventId + '\'' +
                ", parentId='" + parentId + '\'' +
                ", eventName='" + eventName + '\'' +
                ", price='" + price + '\'' +
                ", unit='" + unit + '\'' +
                ", isDel='" + isDel + '\'' +
                ", sortNum='" + sortNum + '\'' +
                ", describe='" + describe + '\'' +
                ", type='" + type + '\'' +
                ", childList=" + childList +
                '}';
    }
}
```
Dao接口
```
List<CompensationTree> getTree(String type);
```

xml
```
<!-- Compensation Bean -->
<resultMap id="BaseResultMap" type="Compensation">
	<id column="ID" property="id" jdbcType="VARCHAR"/>
	<id column="PARENT_DATA_ID" property="parentDataId" jdbcType="VARCHAR"/>
	<result column="EVENT_ID" property="eventId" jdbcType="VARCHAR"/>
	<result column="PARENT_ID" property="parentId" jdbcType="VARCHAR"/>
	<result column="EVENT_NAME" property="eventName" jdbcType="VARCHAR"/>
	<result column="PRICE" property="price" jdbcType="VARCHAR"/>
	<result column="UNIT" property="unit" jdbcType="VARCHAR"/>
	<result column="DESCRIBE" property="describe" jdbcType="VARCHAR"/>
	<result column="TYPE" property="type" jdbcType="VARCHAR"/>
</resultMap>

<resultMap id="CompensationResult" type="Compensation" extends="BaseResultMap">
	<collection property="childList" javaType="java.util.ArrayList" column="{eventId=EVENT_ID,type=TYPE}"
				ofType="Compensation" select="selectChildList"></collection>
</resultMap>

<resultMap id="CompensationTreeResult" type="CompensationTree"
		   extends="CompensationResult">
		   <!-- 传入两个参数进行子节点查询 -->
	<collection property="childList" javaType="java.util.ArrayList" column="{eventId=EVENT_ID,type=TYPE}"
				ofType="Compensation" select="selectChildList"></collection>
</resultMap>

<sql id="Base_Column_List">
	SELECT BXB.ID, BXB.EVENT_ID, BXB.PARENT_ID,BXBT.ID PARENT_DATA_ID, BXB.EVENT_NAME, BXB.PRICE, BXB.UNIT,
	BXB.IS_DEL, BXB.SORT_NUM, BXB.DESCRIBE, BXB.TYPE FROM
	<include refid="tbl_name"/>
</sql>

<sql id="tbl_name">
	B_XTXC_BUCWF BXB
</sql>

<select id="getTree" resultMap="CompensationTreeResult">
	select t.ID, t.EVENT_ID, t.PARENT_ID, t.EVENT_NAME, t.PRICE, t.UNIT,
	t.IS_DEL, t.SORT_NUM, t.DESCRIBE, t.TYPE,BXBT.ID PARENT_DATA_ID from
	(
	select * from B_XTXC_BUCWF bxb where 1 = 1
	AND
	((bxb.PARENT_ID = '2' AND bxb.TYPE = #{type}) OR (bxb.PARENT_ID = '1' AND bxb.TYPE = #{type})
	)) t
	LEFT JOIN B_XTXC_BUCWF BXBT ON (t.PARENT_ID = BXBT.EVENT_ID AND t.TYPE = BXBT.TYPE)
</select>
	
<select id="selectChildList" resultMap="CompensationResult">
	<include refid="Base_Column_List"/>
	LEFT JOIN B_XTXC_BUCWF BXBT ON BXBT.EVENT_ID = BXB.PARENT_ID AND BXBT.TYPE = BXB.TYPE
	WHERE 1 = 1
	AND BXB.PARENT_ID = #{eventId} AND BXB.TYPE = #{type}
</select>

<!-- 查询最顶层信息，条件要在CONNECT BY 子句中带过去 -->
<select id="getTop" resultMap="CompensationMap">
	 SELECT * FROM( SELECT ID, EVENT_ID, PARENT_ID, EVENT_NAME, TYPE, LEVEL le FROM B_XTXC_BUCWF START WITH (EVENT_ID = (select EVENT_ID from B_XTXC_BUCWF bx where bx.id=#{id}) AND TYPE = (select type from B_XTXC_BUCWF bx where bx.id=#{id})) CONNECT BY EVENT_ID = PRIOR PARENT_ID AND TYPE = PRIOR TYPE) T WHERE T .le = ( SELECT MAX (LEVEL) FROM B_XTXC_BUCWF START WITH (EVENT_ID = (select EVENT_ID from B_XTXC_BUCWF bx where bx.id=#{id}) AND TYPE = (select type from B_XTXC_BUCWF bx where bx.id=#{id})) CONNECT BY EVENT_ID = PRIOR PARENT_ID AND TYPE = PRIOR TYPE )
</select>
```
查询结构
```
"msg": [
  {
    "id": "9998",
    "isNewRecord": false,
    "childsList": [
      
    ],
    "eventId": "2",
    "parentId": null,
    "parentName": null,
    "neirmxlj": "打谷晒场",
    "neirbm": null,
    "neirmx": "打谷晒场",
    "paix": 0
  },
  {
    "id": "7231",
    "isNewRecord": false,
    "childsList": [
      {
        "id": "7247",
        "isNewRecord": false,
        "childsList": [
          
        ],
        "eventId": "0302",
        "parentId": "03",
        "parentName": null,
        "neirmxlj": "违法架（埋）设管（杆）线",
        "neirbm": "穿越",
        "neirmx": null,
        
      },
      {
        "id": "7246",
        "isNewRecord": false,
        "childsList": [
          
        ],
        "eventId": "0301",
        "parentId": "03",
        "parentName": null,
        "neirmxlj": "违法架（埋）设管（杆）线",
        "neirbm": "跨越",
        "neirmx": null,
        "paix": 0
      }
    ],
    "eventId": "03",
    "parentId": null,
    "parentName": null,
    "neirmxlj": "违法架（埋）设管（杆）线",
    "neirbm": null,
    "neirmx": "违法架（埋）设管（杆）线",
    "paix": 0
  }
]
```

### 读取txt文件

解析流对象
```java
InputStream stream = ermftpUtil.getStream(gpsUrl);
if (null == stream) { // 解析失败
	ermftpUtil.closeAll(stream, null);
	return null;
}
String dataline = "";
in = new InputStreamReader(stream, "UTF-8");
LineNumberReader reader = new LineNumberReader(in);
while ((dataline = reader.readLine()) != null) {
	GPS gps = new GPS();
	String[] lineData = dataline.split(",");
}
// 关闭流
```
```java
/**
 * 返回字节流流对象
 */
public InputStream getStream(String path) throws IOException, FtpProtocolException {
	InputStream is = null;
	ServletOutputStream os = null;
	this.connectServer(Global.getConfig("ewmftp.ip"), Global.getConfig("ewmftp.port"),
			Global.getConfig("ewmftp.username"), Global.getConfig("ewmftp.password"));
	is = ftpClient.getFileStream(path);
	return is;
}
```

### 写txt文件到ftp
```java

/**
 * 向ftp写txt文件
 *
 * @param txtList
 * @param remotefilename
 * @param path
 * @throws IOException
 */
public boolean writeToText(List<String> txtList, String remotefilename, String path) throws IOException {
	OutputStream os = null;
	if (null != txtList && txtList.size() > 0) {
		try {
			this.connectServer(Global.getConfig("ewmftp.ip"), Global.getConfig("ewmftp.port"),
					Global.getConfig("ewmftp.username"), Global.getConfig("ewmftp.password"));
			if (path.length() != 0) {
				if (!isDirExist(path.replace("\\", "/"))) {
					createDir(path.replace("\\", "/"));
				}
			}
			try {
				os = ftpClient.putFileStream(path + "/" + remotefilename);
				for (String lineDate : txtList) {
					byte[] b = lineDate.getBytes();
					for (int i = 0; i < b.length; i++) {
						os.write(b[i]);
					}
				}
			} catch (FtpProtocolException e) {
				e.printStackTrace();
			}
		} catch (Exception ex) {
			ex.printStackTrace();
			throw new RuntimeException(ex);
		} finally {
			if (null != os)
				os.close();
			ftpClient.close();
		}
		return true;
	}
	return false;
}
```

### window-jdk，tomcat，maven环境便令配置
![a](https://volc1612.gitee.io/blog/images/java/a.png)
![b](https://volc1612.gitee.io/blog/images/java/b.png)


### jdk8的lambda表达式forEach

#### list遍历，map分组，list过滤

Student实体
```java
public class Student {

    private String name;
    private String gender;
    private int age;
    private Long hight;
    private int sex;

    public Student() {
    }

    public Long getHight() {
        return hight;
    }

    public void setHight(Long hight) {
        this.hight = hight;
    }

    public int getSex() {
        return sex;
    }

    public void setSex(int sex) {
        this.sex = sex;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Student(int age, String name) {
        super();
        this.age = age;
        this.name = name;
    }

    public Student(String name, String gender, int age) {
        super();
        this.age = age;
        this.name = name;
        this.gender = gender;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    // 设置 年龄和性别的拼接，得出相应分组
    public Long getIwantStudent() {
        return Long.valueOf(this.sex + this.age);
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.*;
import java.util.stream.Collectors;

public class Group {
    public static void main(String[] args) throws JsonProcessingException {

        ObjectMapper mapper = new ObjectMapper();
        List<Student> lists = Arrays.asList(new Student("zhangsan", "gril", 124),
                new Student("zhangsan", "gril", 123), new Student("wangwu", "man", 123));
        System.out.println("json对象:" + mapper.writeValueAsString(lists));
        // 根据list集合中元素的某个属性分组
        Map<String, List<Student>> result = lists.stream().collect(Collectors.groupingBy(Student::getName));
        System.out.println("单个属性值分组:" + result);
        // 根据list集合中元素的某个属性分组计数
        Map<String, Long> result1 = lists.stream().collect(Collectors.groupingBy(Student::getName, Collectors.counting()));
        System.out.println("单个属性值分组并计数:" + result1);
        // 根据分组的key值对结果进行排序、放进另一个map中并输出
        Map<String, Long> xMap = new HashMap<>();
        result1.entrySet().stream().sorted(Map.Entry.<String, Long>comparingByKey().reversed()) //reversed不生效
                .forEachOrdered(x -> xMap.put(x.getKey(), x.getValue()));
        System.out.println("单个属性值分组后对key值进行排序:" + xMap);
        // 分组，并统计其中一个属性值得sum或者avg:id总和
        Map<String, Integer> result3 = lists.stream().collect(
                Collectors.groupingBy(Student::getName, Collectors.summingInt(Student::getAge))
        );
        System.out.println("单个属性值分组后并计算某个属性总和:" + result3);
        // 对list集合中元素的多个属性分组
        Map<String, Map<String, List<Student>>> result4 = lists.stream().collect(Collectors.groupingBy(Student::getName, Collectors.groupingBy(Student::getGender)));
        System.out.println("多个属性值分组:" + result4);

        List<Student> allList = new ArrayList<>();
        //添加集合信息 省去。。。
        Student st1 = new Student();
        st1.setAge(20);
        st1.setHight(178L);
        st1.setSex(0);
        st1.setName("韩梅梅");
        allList.add(st1);
        Student st11 = new Student();
        st11.setAge(20);
        st11.setHight(168L);
        st11.setSex(1);
        st11.setName("马冬梅");
        allList.add(st11);

        Student st2 = new Student();
        st2.setAge(21);
        st2.setHight(179L);
        st2.setSex(2);
        st2.setName("李磊");
        allList.add(st2);
        /**
         * 需求根据年龄分组，如果一组中只有一条数据则取出，如果有多条数据，再根据性别为0的条件取出第一条数据返回
         */
        // 1. 先根据年龄分组
        Map<Integer, List<Student>> collect = allList.stream().collect(Collectors.groupingBy(Student::getAge));
        // 2. 遍历map
        List<Student> students = new ArrayList<Student>();
        collect.forEach((k, v) -> {
            if (v.size() == 1) { // 如果一条则取出
                students.add(v.get(0));
            } else { // 如果有多条，再根据年龄进行筛选list
                // 根据条件过滤,取第一条
                students.add(v.stream().filter(s -> s.getSex() == 0).findFirst().orElse(null));
            }
        });
        System.out.println(students);
    }
}

```