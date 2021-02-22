---
layout: post
title:  "[DarkCON CTF] WTF PHP"
description: Write-up Web Exploitation DarkCON CTF
tags: writeup
---
# Web Vulnerability: File upload

Đề bài:

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-[darkCON-CTF]-WTF-PHP-1.png)  

Bắt tay vào xem thử như nào thôi

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-[darkCON-CTF]-WTF-PHP-2.png)  
Vừa nhìn vào thì đầu mình đã nhảy số rằng bài này sẽ đi khai thác lỗ hổng `File upload`. Vẫn như thói quen thì ban đầu mình view source của web lên và phát hiện được author đã comment code bên trong:  

![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-[darkCON-CTF]-WTF-PHP-3.png)  
Code cũng khá là đơn giản ban đầu web sẽ cho upfile bất kì không hề filter :D và file bắt buộc phải nhỏ hơn 1048576B. Tiếp đó nếu upload thành công thì file sẽ tạo ra link và move đến dir `/upload/[random name file], ngược lại xuất ra lỗi

Như vậy với đề bài trên mục tiêu của ta sẽ đi đến folder /etc và ở đây sẽ có file flag.txt.  
Mình có thử upload một file hình và chèn vào đó là code php `echo "hello" và vẫn upload bình thường và code cũng được execute luôn. 
![image](/_img/2021-02-22-[darkCON-CTF]-WTF-PHP-4.png)  
Đến đây thì mọi việc dễ dàng hơn rồi! Tiếp đó mình dùng hàm scandir để đọc hết các folder trên server vào cat flag và đây là code của mình:  

```
<?php
	$dir    = '../../../../etc';
	$files = scandir($dir);
	print_r($files);
	print(file_get_contents('../../../../etc/f1@g.txt'));
?>
//flag: darkCON{us1ng_3_y34r_01d_bug_t0_byp4ss_d1s4ble_funct10n}
```
Thành quả  
![image](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-22-[darkCON-CTF]-WTF-PHP-5.png) 
Bài này là bài khá dễ, file được upload lên server mà không hề filter format file nên rất dễ thực thi code và exploit.  
Qua bài trên mình cũng tìm ra được một số hàm có thể thực thi code php mà mình cần nhớ đó là:  
```
eval();

assert();

system();

exec();

shell_exec();

passthru();

escapeshellcmd();

pcntl_exec();

```
Chán đời, bài dễ vl mà éo chịu làm, tui bùn lắm á mng :)
