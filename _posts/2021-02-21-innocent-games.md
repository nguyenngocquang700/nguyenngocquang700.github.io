---
layout: post
title:  "Innotion games"
description: fsdfds
tags: writeup
---
# Innocent Game?

Đề bài:

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-1.png?raw=true)

Bắt tay vào xem thử như nào thôi

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-2.png?raw=true)
Ở bài này ta thấy web cho phép mình thay đổi icon này thành icon khác, nên ban đầu mình đã ngây thơ thử 1 cách ngẫu nhiên

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-3.png?raw=true)
Oops, show ra lỗi này, trong warning hàm được dùng trong code xuất hiện lỗi là hàm `preg_replace()` . Hàm `preg_replace()`dùng để replace một chuỗi nào đó khới với đoạn `Regular Expression` truyền vào. Hàm này cũng có chức năng tương tự như hàm `str_repalce()`nhưng có sự khác biệt ở đây là một bên dùng regex một bên không dùng

Như vậy để sử dụng replace được ta phải input bằng toán tử `/nội dung

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-4.png?raw=true)
submit phát thử nào!!!

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-5.png?raw=true)
chuẩn bàiii...!!!

Theo như mình tìm hiểu thì ở hàm `preg_replace()` trong php có một lỗi khá nghiêm trọng, khi ta dùng regex là `/e` thì chúng cho phép ta execute code ngay đầu vào input. Như vậy, tiến hành thử bằng Burp Suite nào!

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-6.png?raw=true)
okayy, that's worked. Nhưng chớ vội mừng, tiếp đó mình tiến hành đọc file bằng cách gọi hàm system() và dùng command `cat index.php` nhưng không thể làm được vì hàm này đã filter dấu space và cả dấu nháy kép. Sau một lúc suy nghĩ, mình đã thử truyền parameter vào url vào dùng biến $_GET[] để get value

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-7.png?raw=true)
Oops Flag đây luôn =))

À bên cạnh đó cũng có thể dùng hàm `file_get_contents()` để đọc file nữa

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-8.png?raw=true)
và ra kết quả tương tự

Dưới đây là toàn bộ code file `index.php`

```php
<?

$subject = '🧒 👦 👧 🧑 👱 👨 🧔 👨‍🦰 👨‍🦱 👨‍🦳 👨‍🦲 👩 👩‍🦰 🧑‍🦰 👩‍🦱 🧑‍🦱 👩‍🦳 🧑‍🦳 👩‍🦲 🧑‍🦲 👱‍♀️ 👱‍♂️ 🧓 👴 👵 🙍 🙍‍♂️ 🙍‍♀️ 🙎 🙎‍♂️ 🙎‍♀️ 🙅 🙅‍♂️ 🙅‍♀️ 🙆 ';
$target =  '👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 👶 ';

if (isset($_POST['pattern']) && isset($_POST['needle'])) {
  $pattern = $_POST['pattern'];
  $replacement = $_POST['needle'];
  $replaced = preg_replace($pattern, $replacement, $subject);
}
?>

<?
  $answer = '01010100011100100111100100100000011101000110111100100000011101010110111001100100011001010111001001110011011101000110000101101110011001000010000001101000011011110111011100100000011101110110010100100000011010010110110101110000011011000110010101101101011001010110111001110100011001010110010000100000011101000110100001100101001000000111001001100101011100000110110001100001011000110110010100100000011001100111010101101110011000110111010001101001011011110110111000100001';
  $hint = preg_replace('/0/', '⚫️', $answer); // 
  $hint = preg_replace('/1/', '⚪️', $hint);
?>
  Let's make it all 👶  <br><br><br><br>
  Original: <?= $subject; ?> <br>
  Result:   <?= $replaced; ?> <br>

  <form action="/index.php" method="POST">
    <label for="pattern">Replace</label>
    <input type="text" id="pattern" name="pattern"><br><br>
    <label for="needle">With</label>
    <input type="text" id="needle" name="needle"><br><br>
    <input type="submit" value="Submit">
  </form>
  <? if ($replaced == $target) echo $hint; ?>
</head>
<body>

</body>
</html>
<?php
// Flag{preg_replacep_c4n7_b3_7h15_d4n63r0u5}
?>
```

Vậy làm cách nào để ngăn chặn lỗi này trên php?

![](https://github.com/nguyenngocquang700/nguyenngocquang700.github.io/blob/master/_img/2021-02-21-innocent-games/2021-02-21-innocent-games-9.png?raw=true)

Trên thực tế lỗi này chỉ xuất hiện trên phiên bản thấp của php (<5.5.0). Bên cạnh đó, ta cũng có thể sử dụng hàm `preg_quote()` để trích dẫn các kí tự Regular Expression. Khi sử dụng hàm này thì tất cả các ký tự đặc biệt trong Regular Expression sẽ được thêm ký tự `\` phía trước nó. giúp ngăn chặn việc execute code như trên.

[Exploit PHP Remotely - WAF Rule & Filter Bypass](https://www.secjuice.com/php-rce-bypass-filters-sanitization-waf/)
