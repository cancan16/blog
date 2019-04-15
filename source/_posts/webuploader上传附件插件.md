---
title: webuploader上传附件插件
date: 2017-08-08 15:29:08
update: 2017-08-08 15:29:08
categories: upload
tags: [upload,ftp]
---

### 1 webuploader上传插件
插件下载地址：
http://fex.baidu.com/webuploader/

<!-- more -->
### 2 上传页面
```html
<!-- 引入资源 -->
<link rel="stylesheet" type="text/css" href="${ctxStatic}/webuploader/webuploader.css">
<script type="text/javascript" src="${ctxStatic}/webuploader/webuploader.js"></script>
<!-- 弹出上传附件页面 -->
<div>
<a href="javascript:void(0);" id="annexId" style="text-decoration:underline;margin-left:18px;">添加附件</a>
</div>

	<!-- 上传附件页面 -->
	<div id="annexContentid" style="display:none;">
		<a id="closeAnnexContentId" herf="" class="close-a">
			<span></span>
			<span></span>
		</a >
		<div id="pushId" class="push">
			<div id="uploader">
	            <div style="text-align: center;padding-bottom: 4px;">
	              <div id="attach" style="text-align: center;"></div>
	              <input type="button" value="开始上传" id="upload" class="btn btn-primary" style="padding: 8px;margin-top: 2px;color: #00b7ee;border-radius: 3px;" />
	            </div>
	            <div id="thelist" style="padding:10px;text-align: center;height: 227px;overflow-y: auto;"></div>
	        </div>
		</div>
	</div>

<script>
	// 加载上传附件页面
	$('#annexId').click(function (){
		var $list = $("#thelist");
		// 每次点击上传附件创建新的上传的实例对象
	  	uploader = WebUploader.create({
	      auto:false, //是否自动上传
	      dnd: '#pushId', // 拖拽容器
	      pick: {
	        id: '#attach', 
	        name:"multiFile", //这个地方 name 没什么用，和fileVal 配合使用。 
	        label: '点击选择文件',
	        multiple:true
	      },
	      swf: '${ctxStatic}/webuploader/Uploader.swf',
	      fileVal:'multiFile', //提交到到后台，是要用"multiFile"这个名称属性来取文件的 
	      server: "/cloud/sys/cloud/stream/uploadServlet", // 后台web.xml中配置servlet
	      duplicate:false,
	      resize: false,
	      chunked: true, // 大附件分片处理
	      chunkSize: 5 * 1024 * 1024, // 每片1M
	      chunkRetry:2, //如果失败，则不重试
	      threads:1,//上传并发数。允许同时最大上传进程数。
	      fileNumLimit:10,//上传的文件总数
	      // 禁掉全局的拖拽功能
	      disableGlobalDnd: true
	    });
	    // 当有文件添加进来的时候 
	    uploader.on( "fileQueued", function( file ) {
	      $list.append( "<div id='"+ file.id + "' class='item'>" + 
	        "<h4 class='info'>" + file.name + "</h4><input id='delete"+ file.id +"' type='button' onclick=removeFile('"+file.id+"') style='float:right;' value='刪除'/>" + 
	        "<p class='state'></p>" + 
	      "</div>" );
	    });
    	// 文件上传过程中创建进度条实时显示。
		uploader.on( 'uploadProgress', function( file, percentage ) {
		    var $li = $( '#'+file.id ),
		        $percent = $li.find('.progress .progress-bar');
		    // 避免重复创建
		    if ( !$percent.length ) {
		        $percent = $('<div class="progress progress-striped active" style="width:100%;">' +
		          '<div class="progress-bar" role="progressbar" style="width: 0%">' +
		          '</div>' +
		        '</div>').appendTo( $li ).find('.progress-bar');
		    }

		    $li.find('p.state').text('上传中');

		    $percent.css( 'width', percentage * 100 + '%' );
		});

	    //当所有文件上传结束时触发 
	    uploader.on("uploadFinished",function(){
	    });
	    //当文件上传成功时触发。 
	    uploader.on( "uploadSuccess", function(file ,response) {
	      	$( "#"+file.id ).find("p.state").text("已上传");
	      	$( '#'+file.id ).find('.progress').fadeOut();
	      	// layer.msg('上传成功');
	      	$( "#delete"+file.id ).attr("style","display:none;");
			parent.saveAnnexInfo(file, response);
	    });
	    uploader.on( "uploadError", function( file ) {
	      $( "#"+file.id ).find("p.state").text("上传出错");
	      uploader.cancelFile(file);
	      uploader.removeFile(file,true);
	      uploader.reset();
	    });
	    
	   $("#upload").on("click", function() {
	     uploader.upload();
	   });


		// 弹出上传附件页面
		var mes = $("#annexContentid");
		mes.slideDown();
		// 关闭上传页面
		$("#closeAnnexContentId").click(function(){
			mes.slideUp();
		});
	});
	// 附件页面上传成功后调用此方法用户
	function saveAnnexInfo(file, response){
		// 默认第一个附件为主题
		if($('#title').val().length <= 0)
			$('#title').val(file.name.substring(0,file.name.lastIndexOf('.')));
		// 保存附件信息
		$.ajax({
			type : "POST",
			url : dbmailServer + "/annex/saveAnnexInfo",
			data : JSON.stringify({
				url: response.url,
				name: file.name,
				size: file.size
			}),
			contentType : "application/json; charset=utf-8",
			success : function(data) {
				if(data.success == '0'){
					var fileSize = file.size;
					var bigInfo = "";
					if(fileSize / (1024 * 1024) > 20){
						bigInfo =  "&nbsp;&nbsp;(已超过20M，大附件)";
					}
					$("#files").html(
					$("#files").html()
					+ "<div id='"+data.annexId+"'><a href=" + dbmailServer + "/annex/download?id="+ data.annexId + ">"
					+ file.name
					+ "  </a><a onclick=removeSpan('"
					+ data.annexId
					+ "') href='javascript:void(0)'>&nbsp;&nbsp;删除 </a>" + bigInfo + "</div>");
					var annexIds =  $('#upid').val();
					if(annexIds.length > 0 && annexIds.substring(annexIds.length-1,annexIds.length) != ';') {
						$('#upid').val($('#upid').val() + ';' + data.annexId + ';');
					} else {
						$('#upid').val($('#upid').val() + data.annexId + ';');	
					}
				}
			}
		});
	}
	
	// 在上传文件列表中为文件添加移除文件点击事件
	function removeFile(fileId) {
		uploader.on("uploadStart",function(file){
			var nowDay = getNowFormatDate();
	    	$.ajax({
				type : "POST",
				url : '${fns:getConfig("cloud.link")}'
						+ '/stream/deleteFile',
				data : JSON.stringify({
					path: '${fns:getConfig("ewmfiles.mail.basedir")}' + nowDay
				}),
				contentType : "application/json; charset=utf-8",
				async : false
			});
	    });
		$('#' + fileId).remove();
		uploader.removeFile(fileId);
	 	uploader.cancelFile(fileId);
	}

</script>
```
### 3 上传处理类
在web.xml中配置servlet，UploadServlet为处理类，
需要使用apache的ftputils，sun公司的ftputils很多坑
上传工具类ErmftpUtil
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
UploadServlet上传处理类
```java
import java.io.IOException;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.lang3.math.NumberUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import com.biusoft.core.common.config.Global;
import com.biusoft.core.stream.utils.ERMFTPUtil;
import com.fasterxml.jackson.databind.ObjectMapper;

/**
 * @ClassName UploadServlet
 * @Author volc
 * @Description 上传处理
 * @Date 2017年7月9日 下午4:50:00
 */
public class UploadServlet extends HttpServlet {
	private static final long serialVersionUID = -8619685235661387895L;
	private ObjectMapper objectMapper = new ObjectMapper();
	private static final Logger LOGGER = LoggerFactory.getLogger(UploadServlet.class);
	public static ERMFTPUtil ermftpUtil = null;

	@Override
	public void init() throws ServletException {
	}

	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doOptions(request, response);
	}

	@SuppressWarnings("static-access")
	@Override
	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		InputStream content = null;
		doOptions(request, response);
		this.ermftpUtil = new ERMFTPUtil();
		try {
			boolean isMultipart = ServletFileUpload.isMultipartContent(request);
			if (isMultipart) {
				FileItemFactory factory = new DiskFileItemFactory();
				ServletFileUpload upload = new ServletFileUpload(factory);
				// 得到所有的表单域，它们目前都被当作FileItem
				List<FileItem> fileItems = upload.parseRequest(request);
				@SuppressWarnings("unused")
				String id = "";
				String fileName = "";
				// 如果大于1说明是分片处理,分片个数(当个文件需要上传的次数)
				int chunks = 1;
				int chunk = 0;
				FileItem tempFileItem = null;
				for (FileItem fileItem : fileItems) {
					if (fileItem.getFieldName().equals("id")) {
						// 生成的文件ID
						id = fileItem.getString();
					} else if (fileItem.getFieldName().equals("name")) {
						fileName = new String(fileItem.getString().getBytes("ISO-8859-1"), "UTF-8");
					} else if (fileItem.getFieldName().equals("chunks")) {
						chunks = NumberUtils.toInt(fileItem.getString());
					} else if (fileItem.getFieldName().equals("chunk")) {
						chunk = NumberUtils.toInt(fileItem.getString());
					} else if (fileItem.getFieldName().equals("multiFile")) {
						tempFileItem = fileItem;
					}
				}
				// 将需要分片文件名称存放到session中
				// 保存在服务上传文件名称
				String yfileName = (String) request.getSession().getAttribute("fileName");
				if (StringUtils.isEmpty(yfileName)) { // 新文件
					yfileName = UUID.randomUUID().toString().replaceAll("-", "")
							+ fileName.subSequence(fileName.lastIndexOf("."), fileName.length());
					request.getSession().setAttribute("fileName", yfileName);
				}
				// String filePath = Global.getConfig("ewmfiles.mail.basedir") +
				// "/" + "20170714";
				String filePath = Global.getConfig("ewmfiles.mail.basedir") + "/"
						+ new SimpleDateFormat("yyyyMMdd").format(new Date());
				content = tempFileItem.getInputStream();
				// 上传文件
				ermftpUtil.upload(content, yfileName, filePath);
				ermftpUtil.close();
				// 返回结果
				Map<String, Object> rMap = new HashMap<String, Object>();
				if (chunk == chunks - 1) { // 判断是否是最后一片
					request.getSession().setAttribute("fileName", "");
					rMap.put("success", "0");
					rMap.put("fileName", yfileName); // 资源文件名称
					rMap.put("url", filePath + "/" + yfileName);
					response.getWriter().write(objectMapper.writeValueAsString(rMap));
				}
			}
		} catch (Exception e) {
			ermftpUtil.close();
			LOGGER.error(e.getMessage(), e);
			Map<String, Object> rMap = new HashMap<String, Object>();
			rMap.put("success", "1");
			response.getWriter().write(objectMapper.writeValueAsString(rMap));
		}
	}

	@Override
	public void destroy() {
		super.destroy();
	}
}

```

### 4 FTP处理上传下载

接口

```java
import java.io.InputStream;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import com.biusoft.core.common.utils.ResponseInfo;
import com.biusoft.core.modules.cas.modules.cas_fuj.entity.CasFuj;
import com.biusoft.core.modules.cas.modules.cas_fuj.service.CasFujService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import com.biusoft.core.common.config.Global;
import com.biusoft.core.common.utils.upload.ERMFTPUtil;
import com.biusoft.core.modules.sys.entity.User;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.web.multipart.MultipartFile;

/**
 * @ClassName AnnexController
 * @Author volc
 * @Description 附件操作
 * @Date 2017年6月30日 上午10:57:54
 */
@Controller
@RequestMapping(value = "interf/annex")
public class AnnexController {
    private static final ObjectMapper MAPPER = new ObjectMapper();

    @RequestMapping(value = "uploadfile", method = RequestMethod.POST, produces = "application/json; charset=utf-8")
    @ResponseBody
    public String uploadfile(MultipartFile upfile, HttpServletRequest request, HttpServletResponse response)
            throws Exception {
        ResponseInfo responseInfo = new ResponseInfo();
        InputStream inputStream = upfile.getInputStream();
        if (inputStream.available() > 0) {
            request.setCharacterEncoding("utf-8");
            response.setCharacterEncoding("utf-8");
            ERMFTPUtil util = new ERMFTPUtil();
            String casDir = Global.getConfig("base.gps");// ftp地址
            String oname = upfile.getOriginalFilename();
            String nname = oname;
            String path = casDir + "/" + nname;
            util.upload(upfile.getInputStream(), nname, casDir);
            responseInfo.setInfo("上传成功");
            responseInfo.setSuccess(true);
            Map<String, Object> resultMap = new HashMap<>();
            resultMap.put("allurl", Global.getConfig("resources.base") + path);
            resultMap.put("url", path);
            resultMap.put("id", fileId);
            responseInfo.setMsg(resultMap);
        } else {
            responseInfo.setInfo("文件无效");
            responseInfo.setSuccess(false);
        }
        return MAPPER.writeValueAsString(responseInfo);
    }

}

```

引入jar包
```java
import sun.net.ftp.FtpClient;
import sun.net.ftp.FtpProtocolException;

```

工具类

```java
import java.io.*;
import java.net.InetSocketAddress;
import java.net.SocketAddress;
import java.util.List;
import java.util.StringTokenizer;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;

import com.biusoft.core.common.config.Global;
import sun.net.ftp.FtpClient;
import sun.net.ftp.FtpProtocolException;

public class ERMFTPUtil {

    private FtpClient ftpClient;

    public void connectServer(String ip, String port, String user, String password) {
        try {
            ftpClient = FtpClient.create();
            SocketAddress addr = new InetSocketAddress(ip, Integer.valueOf(port));
            ftpClient.connect(addr);
            ftpClient.login(user, password.toCharArray());
        } catch (Exception ex) {
            ex.printStackTrace();
            throw new RuntimeException(ex);
        }
    }

    /**
     * 本地文件上传方法
     */
    public void upload(InputStream is, String remotefilename, String path) throws IOException {
        OutputStream os = null;
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
            } catch (FtpProtocolException e) {
                e.printStackTrace();
            }
            byte[] bytes = new byte[1024];
            int c;
            while ((c = is.read(bytes)) != -1) {
                os.write(bytes, 0, c);
            }
        } catch (Exception ex) {
            ex.printStackTrace();
            throw new RuntimeException(ex);
        } finally {
            is.close();
            os.close();
            ftpClient.close();
        }
    }

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


    public void download(String path, String fileName, HttpServletResponse response) throws IOException {
        InputStream is = null;
        ServletOutputStream os = null;
        try {
            this.connectServer(Global.getConfig("ewmftp.ip"), Global.getConfig("ewmftp.port"),
                    Global.getConfig("ewmftp.username"), Global.getConfig("ewmftp.password"));
            try {
                is = ftpClient.getFileStream(path);
            } catch (FtpProtocolException e) {
                e.printStackTrace();
            }

            response.setContentType("application/x-msdownload");
            response.setHeader("Content-Disposition",
                    "attachment;" + " filename=" + new String(fileName.getBytes("GBK"), "ISO8859-1"));

            os = response.getOutputStream();
            byte[] bytes = new byte[1024];
            int c;
            while ((c = is.read(bytes)) != -1) {
                os.write(bytes, 0, c);
            }
        } catch (Exception ex) {
            ex.printStackTrace();
            throw new RuntimeException(ex);
        } finally {
            is.close();
            os.close();
            ftpClient.close();
        }
    }

    public boolean isDirExist(String path) {
        try {
            ftpClient.changeDirectory(path);
        } catch (Exception e) {
            return false;
        }

        return true;
    }

    public void createDir(String path) throws Exception {
        ftpClient.setAsciiType();
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
        ftpClient.setBinaryType();
    }

    /**
     * 返回字节流流对象
     */
    public InputStream getStream(String path) throws IOException {
        InputStream is = null;
        ServletOutputStream os = null;
        try {
            this.connectServer(Global.getConfig("ewmftp.ip"), Global.getConfig("ewmftp.port"),
                    Global.getConfig("ewmftp.username"), Global.getConfig("ewmftp.password"));
            is = ftpClient.getFileStream(path);
        } catch (Exception ex) {
            ex.printStackTrace();
            throw new RuntimeException(ex);
        }
        return is;
    }

    /**
     * 关闭所有连接对象
     */
    public void closeAll(InputStream in, OutputStream out) {
        try {
            ftpClient.close();
            if (null != in)
                in.close();
            if (null != out)
                out.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

多文件上传接口

```
@ResponseBody
@RequestMapping(value ="/MultiUpload", method = RequestMethod.POST)
public Map<String, Object> MultiUpload(HttpServletRequest request, MultipartHttpServletRequest multiRequest) throws IOException {
	Map<String, Object> responseInfo = new HashMap<String, Object>();
	try {
		CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver(request.getSession().getServletContext());
		//判断 request 是否有文件上传,即多部分请求
		if (multipartResolver.isMultipart(request)) {
			List<MultipartFile> files= multiRequest.getFiles("files");
			List<Map<String, String>> resultList = Lists.newArrayList();
			if(files != null && !files.isEmpty()){
				for(int i = 0; i < files.size(); i++){
					Map<String, String> map = Maps.newHashMap();
					MultipartFile file = files.get(i);  
					//保存文件  
					String fileId = uploadFile(request, file);
					map.put("id", fileId);
					resultList.add(map);
				} 
			} 
			if(resultList.size() > 0){
				responseInfo.put("objs", JSONArray.parseArray(JSON.toJSONString(resultList)));
				responseInfo.put("msg", "成功！");
				responseInfo.put("datacount", resultList.size());
			}else{
				responseInfo.put("objs", JSONArray.parseArray(JSON.toJSONString(new ArrayList<String>())));
				responseInfo.put("msg", "无数据返回！");
				responseInfo.put("datacount", 0);
			}
		}
		responseInfo.put("success", true);
	} catch (Exception e) {
		e.printStackTrace();
		responseInfo.put("objs", JSONArray.parseArray(JSON.toJSONString(new ArrayList<String>())));
		responseInfo.put("msg", "失败!");
		responseInfo.put("success", false);
		responseInfo.put("datacount", 0);
	}
	return responseInfo;
}
```
