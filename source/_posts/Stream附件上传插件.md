---
title: Stream附件上传插件
date: 2017-07-27 13:12:40
update: 2017-07-27 13:12:40
categories: upload
tags: [upload]
---

### 1 Stream附件上传插件
插件下载地址：https://git.oschina.net/jiangdx/stream.git
使用参考：http://www.twinkling.cn/
<!-- more -->
引入jar包

```
<!-- S stream附件上传插件 -->
<dependency>
	<groupId>org.json</groupId>
	<artifactId>json</artifactId>
	<version>20090211</version>
</dependency>

<!-- ftp连接包 -->
<!-- https://mvnrepository.com/artifact/commons-net/commons-net -->
<dependency>
	<groupId>commons-net</groupId>
	<artifactId>commons-net</artifactId>
	<version>3.3</version>
</dependency>
<!-- E -->
```

#### 1.1 ftp上传工具类
```java

import java.io.Closeable;
import java.io.IOException;
import java.io.InputStream;
import java.net.SocketException;
import java.util.StringTokenizer;
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.net.ftp.FTP;
import org.apache.commons.net.ftp.FTPClient;
import org.apache.commons.net.ftp.FTPClientConfig;
import org.apache.commons.net.ftp.FTPFile;
import org.apache.commons.net.ftp.FTPReply;
import com.biusoft.core.common.config.Global;

/**
 * @param <FtpClient>
 * @ClassName ERMFTPUtil
 * @Author volc
 * @Description 上传文件
 * @Date 2017年6月27日 下午5:38:50
 */
public class ERMFTPUtil {
	static final int BUFFER_LENGTH = 10240;
	@SuppressWarnings("unused")
	private static String strencoding;
	private static FTPClient ftpClient;
	private static String ip; // 服务器IP地址
	private static String userName; // 用户名
	private static String userPwd; // 密码
	private static String port; // 端口号
	private static String path; // 读取文件的存放目录

	/**
	 * @param ftpClient
	 * @param ip
	 * @param userName
	 * @param userPwd
	 * @param port
	 * @param path
	 *            默认连接/mail/files/email/email目录
	 */
	public ERMFTPUtil(String path) {
		ip = Global.getConfig("ewmftp.ip");
		userName = Global.getConfig("ewmftp.username");
		userPwd = Global.getConfig("ewmftp.password");
		port = Global.getConfig("ewmftp.port");
		ERMFTPUtil.path = path;
		connectServer(ip, port, userName, userPwd, path);
	}

	/**
	 * 连接ftp
	 */
	public ERMFTPUtil() {
		ip = Global.getConfig("ewmftp.ip");
		userName = Global.getConfig("ewmftp.username");
		userPwd = Global.getConfig("ewmftp.password");
		port = Global.getConfig("ewmftp.port");
		if (null == ftpClient || !ftpClient.isConnected()) {
			reSet();
		}
	}

	/**
	 * @MethodName connectServer
	 * @Author volc
	 * @Description 连接ftp
	 * @Date 2017年6月28日 上午11:55:30
	 */
	public static void connectServer(String ip, String port, String userName, String userPwd, String path) {
		ftpClient = new FTPClient();
		try {
			// 连接
			ftpClient.connect(ip, Integer.valueOf(port));
			// 设置连接属性
			ftpClient.setControlEncoding("GBK");
			FTPClientConfig conf = new FTPClientConfig(FTPClientConfig.SYST_NT);
			conf.setServerLanguageCode("zh");
			// 登录
			ftpClient.login(userName, userPwd);
			// 检测连接是否成功
			int reply = ftpClient.getReplyCode();

			if (!FTPReply.isPositiveCompletion(reply)) {
				ftpClient.disconnect();
				System.err.println("FTP server refused connection.");
			}
			// 设置二进制方式传输
			ftpClient.setFileType(FTP.BINARY_FILE_TYPE);
			ftpClient.enterLocalPassiveMode();
			if (StringUtils.isNotBlank(path)) {
				// 跳转到指定目录
				ftpClient.changeWorkingDirectory(path);
			}
		} catch (SocketException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	/**
	 * @MethodName close
	 * @Author volc
	 * @Description 关闭连接
	 * @Date 2017年6月28日 上午11:59:30
	 */
	public static void close() {
		if (ftpClient.isConnected()) {
			try {
				ftpClient.logout();
				ftpClient.disconnect();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}

	/**
	 * @MethodName reSet
	 * @Author volc
	 * @Description 重新连接ftp
	 * @Date 2017年6月28日 上午11:55:46
	 */
	public static void reSet() {
		strencoding = "GBK";
		connectServer(ip, port, userName, userPwd, path);
	}

	/**
	 * @MethodName upload
	 * @Author volc
	 * @Description 上传文件 PATH: /mail/files/email/email/20170628
	 * @Date 2017年6月27日 下午5:38:31
	 */
	public static void upload(InputStream input, String fileName, String path) throws IOException {
		// OutputStream out = null;
		try {
			if (!ftpClient.isConnected()) {
				reSet();
			}
			ftpClient.makeDirectory(path);
			ftpClient.changeWorkingDirectory(path);
			// ftpClient.storeFile(fileName, input);
			ftpClient.appendFile(fileName, input);
			// out = ftpClient.appendFileStream(fileName);
			// final byte[] bytes = new byte[BUFFER_LENGTH];
			// int read = 0;
			// while ((read = input.read(bytes)) != -1) {
			// out.write(bytes, 0, read);
			// }
			close(input);
			// close(out);
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			// 上传后先不断开连接
			// if (ftpClient != null ) {
			// }
		}
	}

	/**
	 * @throws @MethodName
	 *             isDirExist
	 * @Author volc
	 * @Description 判断目录是否存在 path :/mail/files/email/email/20170627
	 * @Date 2017年6月28日 上午11:58:07
	 */
	public static boolean isDirExist(String path) {
		try {
			FTPFile[] listFiles = ftpClient.listFiles(path);
			int length = listFiles.length;
			if (length == 0) {
				return false;
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
		return true;
	}

	/**
	 * @throws IOException
	 * @MethodName isFileExist
	 * @Author volc
	 * @Description 判断文件是否存在
	 * @Date 2017年7月12日 下午6:04:40
	 */
	public static boolean isFileExist(String path, String fileName) throws IOException {
		ftpClient.changeWorkingDirectory(path);
		InputStream inputStream = ftpClient.retrieveFileStream(fileName);
		return null == inputStream ? false : true;
	}

	public static void createDir(String path) throws Exception {
		StringTokenizer s = new StringTokenizer(path, "/");
		s.countTokens();
		String pathName = "";
		while (s.hasMoreElements()) {
			pathName = pathName + "/" + (String) s.nextElement();
			if (!isDirExist(pathName.replace("\\", "/"))) {
				System.out.println("创建文件夹:" + pathName);
				ftpClient.makeDirectory(pathName);
			}
		}
	}

	/**
	 * @MethodName createFolder
	 * @Author volc
	 * @Description 每次上传上传附件时，单独创建新对象调用此方法用来创建新的文件夹 这里的上传不需要执行此方法
	 * @Date 2017年6月15日 下午9:58:32
	 */
	public static void createFolder(String path) {
		if (path.length() != 0) {
			if (!isDirExist(path.replace("\\", "/"))) {
				try {
					createDir(path.replace("\\", "/"));
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
	}

	/**
	 * @throws IOException
	 * @throws FtpProtocolException
	 * @MethodName createFile
	 * @Author volc
	 * @Description 向ftp创建空文件 name 文件名称 path文件,返回该文件
	 * @Date 2017年6月28日 上午9:48:48
	 */
	public static void createFile(String name, String path) throws IOException {
		// ERMFTPUtil fptUtil = new ERMFTPUtil();
		ftpClient.makeDirectory(path);
		ftpClient.changeWorkingDirectory(path);
		ftpClient.appendFileStream(name);
		// fptUtil.close();
		// this.connectServer(Global.getConfig("ewmftp.ip"),
		// Global.getConfig("ewmftp.port"),
		// Global.getConfig("ewmftp.username"),
		// Global.getConfig("ewmftp.password"), path);
		// OutputStreamWriter out = new
		// OutputStreamWriter(ftpClient.appendFileStream(name));
		// BufferedWriter pw = new BufferedWriter(out);
		// pw.flush();
		// pw.close();
		// File file = new File(path + File.separator + name);
		// return file;
	}

	/**
	 * @throws IOException
	 * @throws FtpProtocolException
	 * @MethodName getFile
	 * @Author volc
	 * @Description 获取指定目录下所有文件大小 path :mail/files/email/email/20170623
	 * @Date 2017年6月28日 上午10:54:08
	 */
	public static Long getFileSize(String path, String filName) throws IOException {
		ftpClient.changeWorkingDirectory(path);
		FTPFile[] listFiles = ftpClient.listFiles(filName);
		long fileSize = 0;
		if (null != listFiles && listFiles.length > 0) {
			fileSize = listFiles[0].getSize();
		}
		return fileSize;
	}

	/**
	 * 
	 * @MethodName main
	 * @Author volc
	 * @Description 关闭流
	 * @Date 2017年6月28日 下午9:26:37
	 */
	public static void close(Closeable stream) {
		try {
			if (stream != null)
				stream.close();
		} catch (IOException e) {
		}
	}

	/**
	 * @MethodName deleteFile
	 * @Author volc
	 * @Description 删除文件
	 * @Date 2017年7月13日 下午1:06:31
	 */
	public static void deleteFile(String path, String fileName) {
		try {
			ftpClient.changeWorkingDirectory(path);
			ftpClient.deleteFile(fileName);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	@SuppressWarnings("static-access")
	public static void main(String agrs[]) throws Exception {
		ERMFTPUtil fptUtil = new ERMFTPUtil();
		// /*
		// * 1.先创建文件夹 2.再上传文件
		// */
		// String localfile =
		// "C://Users//Administrator//Desktop//8f1b5a64ee0645408fc4f5cb990ce59.png";
		// InputStream inputStream = new FileInputStream(localfile);
		// fptUtil.upload(inputStream, "12345",
		// "/mail/files/email/email/20170703");
		// /**
		// * 2.创建一个空文件
		// */
		// // fptUtil.createFile("123", "/mail/files/email/email/20170628");
		// fptUtil.close();
		fptUtil.deleteFile("/mail/files/email/email/20170714", "9c136347404045698b68a735aee93d83.zip");
	}

}

```
#### 1.2 上传核心类StreamServlet
```java

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.json.JSONException;
import org.json.JSONObject;
import com.biusoft.core.common.config.Global;
import com.biusoft.core.stream.config.Configurations;
import com.biusoft.core.stream.utils.ERMFTPUtil;
import com.biusoft.core.stream.utils.IoUtil;

/**
 * File reserved servlet, mainly reading the request parameter and its file
 * part, stored it.
 */
public class StreamServlet extends HttpServlet {
	private static final long serialVersionUID = -8619685235661387895L;
	/** when the has increased to 10kb, then flush it to the hard-disk. */
	static final int BUFFER_LENGTH = 10240;
	static final String START_FIELD = "start";
	public static final String CONTENT_RANGE_HEADER = "content-range";
	public static ERMFTPUtil ermftpUtil = null;

	@Override
	public void init() throws ServletException {
	}

	/**
	 * Lookup where's the position of this file?
	 */
	@SuppressWarnings("static-access")
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doOptions(req, resp);
		// 用户需要上传的文件生成唯一标识(token)，用于断点续传
		String token = req.getParameter(TokenServlet.TOKEN_FIELD);
		final String fileName = req.getParameter(TokenServlet.FILE_NAME_FIELD);
		int lastIndexOf = fileName.lastIndexOf(".");
		if (lastIndexOf != -1) { // 含有后缀
			fileName.substring(fileName.lastIndexOf("."), fileName.length());
			token = token + fileName.substring(fileName.lastIndexOf("."), fileName.length());
		}
		// final String size = req.getParameter(TokenServlet.FILE_SIZE_FIELD);
		// final String fileName =
		// req.getParameter(TokenServlet.FILE_NAME_FIELD);
		final PrintWriter writer = resp.getWriter();

		/** TODO: validate your token. */

		JSONObject json = new JSONObject();
		long start = 0;
		boolean success = true;
		String message = "";
		String basePath = Global.getConfig("ewmfiles.mail.basedir") + "/"
				+ new SimpleDateFormat("yyyyMMdd").format(new Date());
		try {
			// File f = IoUtil.getTokenedFile(token);
			// start = f.length();
			this.ermftpUtil = new ERMFTPUtil();
			start = ermftpUtil.getFileSize(basePath, token);
			/** file size is 0 bytes. */
			// if (token.endsWith("_0") && "0".equals(size) && 0 == start)
			// f.renameTo(IoUtil.getFile(fileName));
		} catch (FileNotFoundException fne) {
			message = "Error: " + fne.getMessage();
			success = false;
		} finally {
			try {
				if (success)
					json.put(START_FIELD, start);
				json.put(TokenServlet.SUCCESS, success);
				json.put(TokenServlet.MESSAGE, message);
				// 文件访问资存放路径和原始名称
				json.put("url", basePath + "/" + token); // 路径
			} catch (JSONException e) {
			}
			writer.write(json.toString());
			IoUtil.close(writer);
		}
	}

	@SuppressWarnings("static-access")
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doOptions(req, resp);
		// 临时文件名称
		String token = req.getParameter(TokenServlet.TOKEN_FIELD);
		// 原始文件名称
		final String fileName = req.getParameter(TokenServlet.FILE_NAME_FIELD);
		int lastIndexOf = fileName.lastIndexOf(".");
		if (lastIndexOf != -1) { // 含有后缀
			fileName.substring(fileName.lastIndexOf("."), fileName.length());
			token = token + fileName.substring(fileName.lastIndexOf("."), fileName.length());
		}

		Range range = IoUtil.parseRange(req);

		//
		OutputStream out = null;
		InputStream content = null;
		final PrintWriter writer = resp.getWriter();

		/** TODO: validate your token. */

		JSONObject json = new JSONObject();
		long start = 0;
		boolean success = true;
		String message = "";
		// S ==========上传至ftp
		// 用户需要上传的文件
		String basePath = Global.getConfig("ewmfiles.mail.basedir") + "/"
				+ new SimpleDateFormat("yyyyMMdd").format(new Date());
		String fileName2 = "";
		// 在非ftp上生成临时的名称为token的空文件用于存放资源
		// 根据每个文件的唯一token确定ftp资源的文件，并续传到此文件中
		// ermftpUtil.createFile(token, basePath);
		try {
			long ftpLength = ermftpUtil.getFileSize(basePath, token);
			if (ftpLength != range.getFrom()) {
				/** drop this uploaded data */
				throw new StreamException(StreamException.ERROR_FILE_RANGE_START);
			}
			content = req.getInputStream();
			// S 上传至ftp
			ermftpUtil.upload(content, token, basePath);
			// E
			start = ermftpUtil.getFileSize(basePath, token);
			// E ==========上传至ftp
		} catch (StreamException se) {
			success = StreamException.ERROR_FILE_RANGE_START == se.getCode();
			message = "Code: " + se.getCode();
		} catch (FileNotFoundException fne) {
			message = "Code: " + StreamException.ERROR_FILE_NOT_EXIST;
			success = false;
		} catch (IOException io) {
			message = "IO Error: " + io.getMessage();
			success = false;
		} finally {
			IoUtil.close(out);
			IoUtil.close(content);
			/** rename the file */
			if (range.getSize() == start) {
				ermftpUtil.close();
				/** fix the `renameTo` bug */
				// File dst = IoUtil.getFile(fileName);
				// dst.delete();
				// TODO: f.renameTo(dst);
				// 重命名在Windows平台下可能会失败，stackoverflow建议使用下面这句
				try {
					// 先删除
					IoUtil.getFile(token).delete();
					// 更换token为用户上传的文件名称
					// Files.move(f.toPath(),
					// f.toPath().resolveSibling(fileName));
					// System.out.println("TK: `" + token + "`, NE: `" +
					// fileName + "`");
					System.out.println("TK: `" + fileName2 + "`, NE: `" + fileName + "`");
					/** if `STREAM_DELETE_FINISH`, then delete it. */
					if (Configurations.isDeleteFinished()) {
						IoUtil.getFile(fileName).delete();
					}
				} catch (IOException e) {
					success = false;
					message = "Rename file error: " + e.getMessage();
				}

			}
			try {
				if (success)
					json.put(START_FIELD, start);
				json.put(TokenServlet.SUCCESS, success);
				json.put(TokenServlet.MESSAGE, message);
				// 文件访问资存放路径和原始名称
				json.put("url", basePath + "/" + token); // 路径
			} catch (JSONException e) {
			}

			writer.write(json.toString());
			IoUtil.close(writer);
		}
	}

	@Override
	protected void doOptions(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		resp.setContentType("application/json;charset=utf-8");
		resp.setHeader("Access-Control-Allow-Headers", "Content-Range,Content-Type");
		resp.setHeader("Access-Control-Allow-Origin", Configurations.getCrossOrigins());
		resp.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS");
	}

	@Override
	public void destroy() {
		super.destroy();
	}
}
```

### 1.3 stream配置文件stream-config.properties

```
# file stored repository (Chinese words need ASCII, help tool @http://tool.oschina.net/encode?type=3)
STREAM_FILE_REPOSITORY=

# when the file has uploaded, whether delete it.
STREAM_DELETE_FINISH=false

# this server whether allow other different domain[s] upload file to this server
STREAM_IS_CROSS=false

# allowed domain (PS: flash method need modifying the `crossdomain.xml`)
STREAM_CROSS_ORIGIN=*

# when Browser @http:www.A.com, the file will upload to @STREAM_CROSS_SERVER
STREAM_CROSS_SERVER=http://customers.duapp.com
```

### 1.4 web.xml添加servlet
```
<!-- S stream附件上传 -->
	<servlet>
		<servlet-name>TokenServlet</servlet-name>
		<servlet-class>com.biusoft.core.stream.servlet.TokenServlet</servlet-class>
		<load-on-startup>0</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>TokenServlet</servlet-name>
		<url-pattern>/sys/cloud/stream/tk</url-pattern>
	</servlet-mapping>
	<servlet>
		<servlet-name>StreamServlet</servlet-name>
		<servlet-class>com.biusoft.core.stream.servlet.StreamServlet</servlet-class>
		<load-on-startup>0</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>StreamServlet</servlet-name>
		<url-pattern>/sys/cloud/stream/upload</url-pattern>
	</servlet-mapping>
	<servlet>
		<servlet-name>FormDataServlet</servlet-name>
		<servlet-class>com.biusoft.core.stream.servlet.FormDataServlet</servlet-class>
		<load-on-startup>0</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>FormDataServlet</servlet-name>
		<url-pattern>/sys/cloud/stream/fd</url-pattern>
	</servlet-mapping>
	<!-- E stream附件上传 -->
```

### 1.5 上传附件页面

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ include file="/WEB-INF/views/include/taglib.jsp"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<link href="${ctxStatic}/stream/css/stream-v1.css" rel="stylesheet" type="text/css">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>附件上传</title>
<meta name="keywords" content="www.cnblogs.com/jihua"/>
<script type="text/javascript" src="${ctxStatic}/jquery/jquery-1.8.3.min.js"></script>
</head>
<body>
	<input id="pid" type="hidden" value="${param}" />
	<div id="i_select_files">
	</div>

	<div id="i_stream_files_queue">
	</div>
	<button onclick="javascript:_t.upload();">开始上传</button>|<button onclick="javascript:_t.stop();">停止上传</button>|<button onclick="javascript:_t.cancel();">取消</button>
	<br>
	Messages:
	<div id="i_stream_message_container" class="stream-main-upload-box" style="overflow: auto;height:150px;">
	</div>
	<br>
<script type="text/javascript" src="${ctxStatic}/stream/js/stream-v1.js"></script>
<script type="text/javascript">
/**
 * 配置文件（如果没有默认字样，说明默认值就是注释下的值）
 * 但是，on*（onSelect， onMaxSizeExceed...）等函数的默认行为
 * 是在ID为i_stream_message_container的页面元素中写日志
 */
	var config = {
		browseFileId : "i_select_files", /** 选择文件的ID, 默认: i_select_files */
		browseFileBtn : "<div>请选择文件</div>", /** 显示选择文件的样式, 默认: `<div>请选择文件</div>` */
		dragAndDropArea: "i_select_files", /** 拖拽上传区域，Id（字符类型"i_select_files"）或者DOM对象, 默认: `i_select_files` */
		dragAndDropTips: "<span>把文件(文件夹)拖拽到这里</span>", /** 拖拽提示, 默认: `<span>把文件(文件夹)拖拽到这里</span>` */
		filesQueueId : "i_stream_files_queue", /** 文件上传容器的ID, 默认: i_stream_files_queue */
		filesQueueHeight : 200, /** 文件上传容器的高度（px）, 默认: 450 */
		messagerId : "i_stream_message_container", /** 消息显示容器的ID, 默认: i_stream_message_container */
		multipleFiles: true, /** 多个文件一起上传, 默认: false */
		onRepeatedFile: function(f) {
			alert("文件："+f.name +" 大小："+f.size + " 已存在于上传队列中。");
			return false;
		},
		autoUploading: false, /** 选择文件后是否自动上传, 默认: true */
		// autoRemoveCompleted : true, /** 是否自动删除容器中已上传完毕的文件, 默认: false */
		// maxSize: 104857600, /** 单个文件的最大大小，默认:2G */
//		retryCount : 5, /** HTML5上传失败的重试次数 */
//		postVarsPerFile : { /** 上传文件时传入的参数，默认: {} */
//			param1: "val1",
//			param2: "val2"
//		},
		// swfURL : "${ctxStatic}/stream/swf/FlashUploader.swf", /** SWF文件的位置 */
		tokenURL : "/cloud/sys/cloud/stream/tk", /** 根据文件名、大小等信息获取Token的URI（用于生成断点续传、跨域的令牌） */
		frmUploadURL : "/cloud/sys/cloud/stream/fd;", /** Flash上传的URI */
		uploadURL : "/cloud/sys/cloud/stream/upload",/** HTML5上传的URI */
//		simLimit: 200, /** 单次最大上传文件个数 */
//		extFilters: [".txt", ".rpm", ".rmvb", ".gz", ".rar", ".zip", ".avi", ".mkv", ".mp3"], /** 允许的文件扩展名, 默认: [] */
//		onSelect: function(list) {alert('onSelect')}, /** 选择文件后的响应事件 */
//		onMaxSizeExceed: function(size, limited, name) {alert('onMaxSizeExceed')}, /** 文件大小超出的响应事件 */
//		onFileCountExceed: function(selected, limit) {alert('onFileCountExceed')}, /** 文件数量超出的响应事件 */
//		onExtNameMismatch: function(name, filters) {alert('onExtNameMismatch')}, /** 文件的扩展名不匹配的响应事件 */
		//onCancel : function(file) {alert('Canceled:  ' + file.name)} /** 取消上传文件的响应事件 */
		onComplete : function(file) {
			saveAnnexInfo(file);

		} /** 单个文件上传完毕的响应事件 */
//		onQueueComplete: function() {alert('onQueueComplete')}, /** 所以文件上传完毕的响应事件 */
//		onUploadError: function(status, msg) {alert('onUploadError')} /** 文件上传出错的响应事件 */
//		onDestroy: function() {alert('onDestroy')} /** 文件上传出错的响应事件 */
	};
	var _t = new Stream(config);
</script>
</body>
<script>
<!-- 附件上传成功 -->
function saveAnnexInfo(file){
	layer.msg('上传成功');
	parent.saveAnnexInfo(file);
}
</script>
</html>
```
