---
title: java操作Excel
date: 2016-12-24 10:08:15
update: 2016-12-28 10:08:15
categories: JAVA
tags: [java, excel]
---

### java解析.xlsx或者xls格式数据

<!-- more -->
```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.ss.usermodel.WorkbookFactory;

/**
 * @param <E>
 * @ClassName ReadExcelToList
 * @Description 读取xlsx数据返回list集合
 * @Author youtanzhi
 * @Date 2016年12月23日 下午4:09:30 需要注意的是导入的 poi相关jar包版本号要一致
 * 需要两个jar包:poi和poi-ooxml
 *       <dependency> <groupId>org.apache.poi</groupId>
 *       <artifactId>poi</artifactId>
 *       <version>3.10-FINAL</version> </dependency> <!--
 *       https://mvnrepository.com/artifact/org.apache.poi/poi-ooxml -->
 *       <dependency> <groupId>org.apache.poi</groupId>
 *       <artifactId>poi-ooxml</artifactId>
 *       <version>3.10-FINAL</version> </dependency>
 */
public class ReadExcelToList<E> {

    public List<E> getList(String file_dir, Class<? extends Object> classObj) throws IOException,
	    InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException {
	List<E> dataList = new ArrayList<E>();
	Method[] methods = classObj.getMethods();
	Method m = null;
	List<Method> beanMehtodList = new ArrayList<Method>();
	for (int x = 0; x < methods.length; x++) {
	    m = methods[x];
	    beanMehtodList.add(m);
	}
	Workbook book = null;
	book = getExcelWorkbook(file_dir);
	Sheet sheet = getSheetByNum(book, 0);
	int lastRowNum = sheet.getLastRowNum(); // 每一行含有内容的列的数量，如果lastCellNum大于含有内容列数的个数会报错。
	System.out.println("last number is " + lastRowNum);
	for (int i = 1; i <= lastRowNum; i++) {
	    Row row = null;
	    row = sheet.getRow(i);
	    if (row != null) {
		System.out.println("reading line is " + i);
		int lastCellNum = row.getLastCellNum();
		System.out.println("lastCellNum is " + lastCellNum);
		Cell cell = null;
		// 创建泛型对象
		Object newInstance = classObj.newInstance();
		for (int j = 0; j < lastCellNum; j++) {
		    cell = row.getCell(j);
		    String value = getValue(cell);
		    if (j == 0) {
			Method method = beanMehtodList.get(4);
			method.invoke(newInstance, value);
		    }
		}
		dataList.add((E) newInstance);
	    }
	}
	return dataList;
    }

    public static Sheet getSheetByNum(Workbook book, int number) {
	Sheet sheet = null;
	try {
	    sheet = book.getSheetAt(number);
	    // if(sheet == null){
	    // sheet = book.createSheet("Sheet"+number);
	    // }
	} catch (Exception e) {
	    throw new RuntimeException(e.getMessage());
	}
	return sheet;
    }

    public static Workbook getExcelWorkbook(String filePath) throws IOException {
	Workbook book = null;
	File file = null;
	FileInputStream fis = null;

	try {
	    file = new File(filePath); // filePath:E:/f/t.xls
	    if (!file.exists()) {
		throw new RuntimeException("文件不存在");
	    } else {
		fis = new FileInputStream(file);
		book = WorkbookFactory.create(fis);
	    }
	} catch (Exception e) {
	    throw new RuntimeException(e.getMessage());
	} finally {
	    if (fis != null) {
		fis.close();
	    }
	}
	return book;
    }

    public String getValue(Cell hssfCell) {
	if (hssfCell.getCellType() == hssfCell.CELL_TYPE_BOOLEAN) {
	    // 返回布尔类型的值
	    return String.valueOf(hssfCell.getBooleanCellValue());
	} else if (hssfCell.getCellType() == hssfCell.CELL_TYPE_NUMERIC) {
	    // 返回数值类型的值
	    Object inputValue = null;// 单元格值
	    Long longVal = Math.round(hssfCell.getNumericCellValue());
	    Double doubleVal = hssfCell.getNumericCellValue();
	    if (Double.parseDouble(longVal + ".0") == doubleVal) { // 判断是否含有小数位.0
		inputValue = longVal;
	    } else {
		inputValue = doubleVal;
	    }
	    DecimalFormat df = new DecimalFormat("#.####"); // 格式化为四位小数，按自己需求选择；
	    return String.valueOf(df.format(inputValue)); // 返回String类型
	} else {
	    // 返回字符串类型的值
	    return String.valueOf(hssfCell.getStringCellValue());
	}
    }
}
```

### 使用
```java
public void imporDataFromExcel(Map<String, Object> param) {
	String file = "E:/f/t.xls";
	StationRegister stationRegister = new StationRegister();
	ReadExcelToList<StationRegister> readExcelToList = new ReadExcelToList<StationRegister>();
	List<StationRegister> list = null;
	try {
	    try {
			list = readExcelToList.getList(file, stationRegister.getClass());
	    } catch (IllegalArgumentException e) {
			e.printStackTrace();
	    } catch (InstantiationException e) {
			e.printStackTrace();
	    } catch (IllegalAccessException e) {
			e.printStackTrace();
	    } catch (InvocationTargetException e) {
			e.printStackTrace();
	    }
	} catch (IOException e) {
	    e.printStackTrace();
	}
}
```

### java导出为.xlsx或者xls

导出.xlsx
```java
import java.io.File;
import java.io.FileOutputStream;
import org.apache.poi.hssf.util.HSSFColor;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.CellStyle;
import org.apache.poi.ss.usermodel.Font;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.ss.util.CellRangeAddress;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

/**
 * @ClassName ReadDataToXlsx2
 * @Description 导出excel.xlsx格式
 * @Author youtanzhi
 * @Date 2016年12月27日 上午10:48:56
 */
public class ReadDataToXlsx2 {

    // 存储路径
    private String excelPath = "D:/data.xlsx";

    public static void main(String[] args) throws Exception {
	ReadDataToXlsx2 excel = new ReadDataToXlsx2();
	if (excel.createExcelFile()) {
	    System.out.println("data.xlsx is created successfully.");
	}
    }

    public boolean createExcelFile() {
	boolean isCreateSuccess = false;
	Workbook workbook = null;
	try {
	    workbook = new XSSFWorkbook();
	} catch (Exception e) {
	    System.out.println("It cause Error on CREATING excel workbook: ");
	    e.printStackTrace();
	}
	if (workbook != null) {
	    Sheet sheet = workbook.createSheet(Constants.SHEETNAME);
	    // 合并单元格(表格标题)
	    CellRangeAddress cra = new CellRangeAddress(0, 1, 0, 10); // 合并单元格的首行、最后一行、首列、最后一列
	    sheet.addMergedRegion(cra);
	    Row row_t = sheet.createRow(0);
	    Cell cell_t = row_t.createCell(0);
	    cell_t.setCellValue("When you're right , no one remembers, when you're wrong ,no one forgets .");
	    // 字段标题
	    Row row0 = sheet.createRow(Constants.TITELHIGHT);
	    for (int i = 0; i < 11; i++) {
		Cell cell_1 = row0.createCell(i, Cell.CELL_TYPE_STRING);
		CellStyle style = getStyle(workbook);
		cell_1.setCellStyle(style);
		cell_1.setCellValue("HELLO" + i + "Column");
		sheet.autoSizeColumn(i);
	    }
	    // 表格内容
	    for (int rowNum = Constants.TITELHIGHT + 1; rowNum < 200; rowNum++) {
		Row row = sheet.createRow(rowNum);
		for (int i = 0; i < 11; i++) {
		    Cell cell = row.createCell(i, Cell.CELL_TYPE_STRING);
		    cell.setCellValue("cell" + String.valueOf(rowNum + 1) + String.valueOf(i + 1));
		}
	    }
	    try {
		FileOutputStream outputStream = new FileOutputStream(excelPath);
		workbook.write(outputStream);
		outputStream.flush();
		outputStream.close();
		isCreateSuccess = true;
	    } catch (Exception e) {
		System.out.println("It cause Error on WRITTING excel workbook: ");
		e.printStackTrace();
	    }
	}
	File sss = new File(excelPath);
	System.out.println(sss.getAbsolutePath());
	return isCreateSuccess;
    }

    private CellStyle getStyle(Workbook workbook) {
	CellStyle style = workbook.createCellStyle();
	style.setAlignment(CellStyle.ALIGN_CENTER);
	style.setVerticalAlignment(CellStyle.VERTICAL_CENTER);
	// 设置单元格字体
	Font headerFont = workbook.createFont(); // 字体
	headerFont.setFontHeightInPoints((short) 14);
	headerFont.setColor(HSSFColor.RED.index);
	headerFont.setFontName("宋体");
	style.setFont(headerFont);
	style.setWrapText(true);

	// 设置单元格边框及颜色
	style.setBorderBottom((short) 1);
	style.setBorderLeft((short) 1);
	style.setBorderRight((short) 1);
	style.setBorderTop((short) 1);
	style.setWrapText(true);
	return style;
    }

    public String getExcelPath() {
	return this.excelPath;
    }

    public void setExcelPath(String excelPath) {
	this.excelPath = excelPath;
    }

    /**
     * @ClassName Constants
     * @Description 表格属性常量
     * @Author youtanzhi
     * @Date 2016年12月27日 上午10:59:06
     */
    public interface Constants {
	public static final String SHEETNAME = "标题1"; // 表格1标题
	public static final int TITELHIGHT = 2; // 标题高度
    }

}
```
导出.xls

```java
import java.io.IOException;
import java.io.OutputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Set;
import java.util.Map.Entry;
import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFRow;
import org.apache.poi.hssf.usermodel.HSSFSheet;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.hssf.util.CellRangeAddress;
import org.apache.poi.ss.usermodel.CellStyle;
import org.apache.poi.ss.usermodel.Font;
import org.apache.poi.ss.usermodel.IndexedColors;

@SuppressWarnings("deprecation")
public class ExcelUtil {
    private static HSSFWorkbook wb;

    private static CellStyle titleStyle; // 标题行样式
    private static Font titleFont; // 标题行字体
    private static CellStyle dateStyle; // 日期行样式
    private static Font dateFont; // 日期行字体
    private static CellStyle headStyle; // 表头行样式
    private static Font headFont; // 表头行字体
    private static CellStyle contentStyle; // 内容行样式
    private static Font contentFont; // 内容行字体

    /**
     * @throws IllegalAccessException
     * @throws IllegalArgumentException
     * @Description: 将Map里的集合对象数据输出Excel数据流
	 * ExportSetInfo实体类包含表格名称、字段、表格数据
     */
    @SuppressWarnings({ "unchecked" })
    public static void export2Excel(ExportSetInfo setInfo) 
	    throws IOException, IllegalArgumentException, IllegalAccessException {
	init();
	Set<Entry<String, List>> set = setInfo.getObjsMap().entrySet();
	String[] sheetNames = new String[setInfo.getObjsMap().size()];
	int sheetNameNum = 0;
	for (Entry<String, List> entry : set) {
	    sheetNames[sheetNameNum] = entry.getKey();
	    sheetNameNum++;
	}
	HSSFSheet[] sheets = getSheets(setInfo.getObjsMap().size(), sheetNames);
	int sheetNum = 0;
	for (Entry<String, List> entry : set) {
	    // Sheet
	    List objs = entry.getValue();
	    // 标题行
	    createTableTitleRow(setInfo, sheets, sheetNum);
	    // 日期行
	    createTableDateRow(setInfo, sheets, sheetNum);
	    // 表头
	    creatTableHeadRow(setInfo, sheets, sheetNum);
	    // 表体
	    String[] fieldNames = setInfo.getFieldNames().get(sheetNum);
	    int rowNum = 3;
	    for (Object obj : objs) {
		HSSFRow contentRow = sheets[sheetNum].createRow(rowNum);
		contentRow.setHeight((short) 300);
		HSSFCell[] cells = getCells(contentRow, setInfo.getFieldNames().get(sheetNum).length);
		int cellNum = 1; // 去掉一列序号，因此从1开始
		if (fieldNames != null) {
		    for (int num = 0; num < fieldNames.length; num++) {
			Object value = ReflectionUtils.invokeGetterMethod(obj, fieldNames[num]);
			cells[cellNum].setCellValue(value == null ? "" : value.toString());
			cellNum++;
		    }
		}
		rowNum++;
	    }
	    adjustColumnSize(sheets, sheetNum, fieldNames); // 自动调整列宽
	    sheetNum++;
	}
	wb.write(setInfo.getOut());
    }

    /**
     * @Description: 初始化
     */
    private static void init() {
	wb = new HSSFWorkbook();

	titleFont = wb.createFont();
	titleStyle = wb.createCellStyle();
	dateStyle = wb.createCellStyle();
	dateFont = wb.createFont();
	headStyle = wb.createCellStyle();
	headFont = wb.createFont();
	contentStyle = wb.createCellStyle();
	contentFont = wb.createFont();

	initTitleCellStyle();
	initTitleFont();
	initDateCellStyle();
	initDateFont();
	initHeadCellStyle();
	initHeadFont();
	initContentCellStyle();
	initContentFont();
    }

    /**
     * @Description: 自动调整列宽
     */
    @SuppressWarnings("unused")
    private static void adjustColumnSize(HSSFSheet[] sheets, int sheetNum, String[] fieldNames) {
	for (int i = 0; i < fieldNames.length + 1; i++) {
	    sheets[sheetNum].autoSizeColumn(i, true);
	}
    }

    /**
     * @Description: 创建标题行(需合并单元格)
     */
    private static void createTableTitleRow(ExportSetInfo setInfo, HSSFSheet[] sheets, int sheetNum) {
	CellRangeAddress titleRange = new CellRangeAddress(0, 0, 0, setInfo.getFieldNames().get(sheetNum).length);
	sheets[sheetNum].addMergedRegion(titleRange);
	HSSFRow titleRow = sheets[sheetNum].createRow(0);
	titleRow.setHeight((short) 800);
	HSSFCell titleCell = titleRow.createCell(0);
	titleCell.setCellStyle(titleStyle);
	titleCell.setCellValue(setInfo.getTitles()[sheetNum]);
    }

    /**
     * @Description: 创建日期行(需合并单元格)
     */
    private static void createTableDateRow(ExportSetInfo setInfo, HSSFSheet[] sheets, int sheetNum) {
	CellRangeAddress dateRange = new CellRangeAddress(1, 1, 0, setInfo.getFieldNames().get(sheetNum).length);
	sheets[sheetNum].addMergedRegion(dateRange);
	HSSFRow dateRow = sheets[sheetNum].createRow(1);
	dateRow.setHeight((short) 350);
	HSSFCell dateCell = dateRow.createCell(0);
	dateCell.setCellStyle(dateStyle);
	dateCell.setCellValue(new SimpleDateFormat("yyyy-MM-dd").format(new Date()));
    }

    /**
     * @Description: 创建表头行(需合并单元格)
     */
    private static void creatTableHeadRow(ExportSetInfo setInfo, HSSFSheet[] sheets, int sheetNum) {
	// 表头
	HSSFRow headRow = sheets[sheetNum].createRow(2);
	headRow.setHeight((short) 350);
	// 序号列
	HSSFCell snCell = headRow.createCell(0);
	snCell.setCellStyle(headStyle);
	snCell.setCellValue("序号");
	// 列头名称
	for (int num = 1, len = setInfo.getHeadNames().get(sheetNum).length; num <= len; num++) {
	    HSSFCell headCell = headRow.createCell(num);
	    headCell.setCellStyle(headStyle);
	    headCell.setCellValue(setInfo.getHeadNames().get(sheetNum)[num - 1]);
	}
    }

    /**
     * @Description: 创建所有的Sheet
     */
    private static HSSFSheet[] getSheets(int num, String[] names) {
	HSSFSheet[] sheets = new HSSFSheet[num];
	for (int i = 0; i < num; i++) {
	    sheets[i] = wb.createSheet(names[i]);
	}
	return sheets;
    }

    /**
     * @Description: 创建内容行的每一列(附加一列序号)
     */
    private static HSSFCell[] getCells(HSSFRow contentRow, int num) {
	HSSFCell[] cells = new HSSFCell[num + 1];

	for (int i = 0, len = cells.length; i < len; i++) {
	    cells[i] = contentRow.createCell(i);
	    cells[i].setCellStyle(contentStyle);
	}
	// 设置序号列值，因为出去标题行和日期行，所有-2
	cells[0].setCellValue(contentRow.getRowNum() - 2);

	return cells;
    }

    /**
     * @Description: 初始化标题行样式
     */
    private static void initTitleCellStyle() {
	titleStyle.setAlignment(CellStyle.ALIGN_CENTER);
	titleStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);
	titleStyle.setFont(titleFont);
	titleStyle.setFillBackgroundColor(IndexedColors.SKY_BLUE.index);
    }

    /**
     * @Description: 初始化日期行样式
     */
    private static void initDateCellStyle() {
	dateStyle.setAlignment(CellStyle.ALIGN_CENTER_SELECTION);
	dateStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);
	dateStyle.setFont(dateFont);
	dateStyle.setFillBackgroundColor(IndexedColors.SKY_BLUE.index);
    }

    /**
     * @Description: 初始化表头行样式
     */
    private static void initHeadCellStyle() {
	headStyle.setAlignment(CellStyle.ALIGN_CENTER);
	headStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);
	headStyle.setFont(headFont);
	headStyle.setFillBackgroundColor(IndexedColors.YELLOW.index);
	headStyle.setBorderTop(CellStyle.BORDER_MEDIUM);
	headStyle.setBorderBottom(CellStyle.BORDER_THIN);
	headStyle.setBorderLeft(CellStyle.BORDER_THIN);
	headStyle.setBorderRight(CellStyle.BORDER_THIN);
	headStyle.setTopBorderColor(IndexedColors.BLUE.index);
	headStyle.setBottomBorderColor(IndexedColors.BLUE.index);
	headStyle.setLeftBorderColor(IndexedColors.BLUE.index);
	headStyle.setRightBorderColor(IndexedColors.BLUE.index);
    }

    /**
     * @Description: 初始化内容行样式
     */
    private static void initContentCellStyle() {
	contentStyle.setAlignment(CellStyle.ALIGN_CENTER);
	contentStyle.setVerticalAlignment(CellStyle.VERTICAL_CENTER);
	contentStyle.setFont(contentFont);
	contentStyle.setBorderTop(CellStyle.BORDER_THIN);
	contentStyle.setBorderBottom(CellStyle.BORDER_THIN);
	contentStyle.setBorderLeft(CellStyle.BORDER_THIN);
	contentStyle.setBorderRight(CellStyle.BORDER_THIN);
	contentStyle.setTopBorderColor(IndexedColors.BLUE.index);
	contentStyle.setBottomBorderColor(IndexedColors.BLUE.index);
	contentStyle.setLeftBorderColor(IndexedColors.BLUE.index);
	contentStyle.setRightBorderColor(IndexedColors.BLUE.index);
	contentStyle.setWrapText(true); // 字段换行
    }

    /**
     * @Description: 初始化标题行字体
     */
    private static void initTitleFont() {
	titleFont.setFontName("华文楷体");
	titleFont.setFontHeightInPoints((short) 20);
	titleFont.setBoldweight(Font.BOLDWEIGHT_BOLD);
	titleFont.setCharSet(Font.DEFAULT_CHARSET);
	titleFont.setColor(IndexedColors.BLUE_GREY.index);
    }

    /**
     * @Description: 初始化日期行字体
     */
    private static void initDateFont() {
	dateFont.setFontName("隶书");
	dateFont.setFontHeightInPoints((short) 10);
	dateFont.setBoldweight(Font.BOLDWEIGHT_BOLD);
	dateFont.setCharSet(Font.DEFAULT_CHARSET);
	dateFont.setColor(IndexedColors.BLUE_GREY.index);
    }

    /**
     * @Description: 初始化表头行字体
     */
    private static void initHeadFont() {
	headFont.setFontName("宋体");
	headFont.setFontHeightInPoints((short) 10);
	headFont.setBoldweight(Font.BOLDWEIGHT_BOLD);
	headFont.setCharSet(Font.DEFAULT_CHARSET);
	headFont.setColor(IndexedColors.BLUE_GREY.index);
    }

    /**
     * @Description: 初始化内容行字体
     */
    private static void initContentFont() {
	contentFont.setFontName("宋体");
	contentFont.setFontHeightInPoints((short) 10);
	contentFont.setBoldweight(Font.BOLDWEIGHT_NORMAL);
	contentFont.setCharSet(Font.DEFAULT_CHARSET);
	contentFont.setColor(IndexedColors.BLUE_GREY.index);
    }

    /**
     * @Description: 封装Excel导出的设置信息
     * @author: 谭又中
     */
    public static class ExportSetInfo {
	@SuppressWarnings("unchecked")
	private LinkedHashMap<String, List> objsMap;

	private String[] titles;

	private List<String[]> headNames;

	private List<String[]> fieldNames;

	private OutputStream out;

	@SuppressWarnings("unchecked")
	public LinkedHashMap<String, List> getObjsMap() {
	    return objsMap;
	}

	/**
	 * @param objMap
	 *            导出数据
	 * 
	 *            泛型 String : 代表sheet名称 List : 代表单个sheet里的所有行数据
	 */
	@SuppressWarnings("unchecked")
	public void setObjsMap(LinkedHashMap<String, List> objsMap) {
	    this.objsMap = objsMap;
	}

	public List<String[]> getFieldNames() {
	    return fieldNames;
	}

	/**
	 * @param clazz
	 *            对应每个sheet里的每行数据的对象的属性名称
	 */
	public void setFieldNames(List<String[]> fieldNames) {
	    this.fieldNames = fieldNames;
	}

	public String[] getTitles() {
	    return titles;
	}

	/**
	 * @param titles
	 *            对应每个sheet里的标题，即顶部大字
	 */
	public void setTitles(String[] titles) {
	    this.titles = titles;
	}

	public List<String[]> getHeadNames() {
	    return headNames;
	}

	/**
	 * @param headNames
	 *            对应每个页签的表头的每一列的名称
	 */
	public void setHeadNames(List<String[]> headNames) {
	    this.headNames = headNames;
	}

	public OutputStream getOut() {
	    return out;
	}

	/**
	 * @param out
	 *            Excel数据将输出到该输出流
	 */
	public void setOut(OutputStream out) {
	    this.out = out;
	}
    }
}
```