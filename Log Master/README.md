# Log Master

CTF: alphaCTF 2022

Category : Forensics

---

The CTF is about figuring out the entry point for a hack, through some logs.

Flag Format: alphaCTF{ATTACKER-IP_TIMEOFTHEATTACK_FILEREAD}

---

The zip contains two files, requests log and mysql queries.

My approach was to filter user agents, guessing an attacker would probably script the attack through anything but a web browser ( assuming he did not change the user-agent ).

```bash
$ grep -v "Mozilla\|Opera" access_log_20220220-002703.log
166.55.88.58 - - [05/Mar/2022:14:17:12 +0100] "GET /apiv2/shop/products HTTP/1.0" 200 5093 "https://shop.aldersonselectronics.com" "curl/7.80.0"
```

Let's look for that exact time in the mysql queries.

```bash
$ grep "2022-Mar-05 14:17" mysql.log
| 2022-Mar-05 14:17:12 | root[root] @  [172.17.0.1] |     8     |     1     |    Query     | SELECT * FROM PRODUCTS WHERE pid='REDACTED' UNION SELECT load_file(FROM_BASE64('L29wdC9zaG9wL3NldHRpbmdzLnB5')) |
# Decode the base64 encoded string
$ echo L29wdC9zaG9wL3NldHRpbmdzLnB5|base64 -d
/opt/shop/settings.py
```

alphaCTF{166.55.88.58_14:17:12_/opt/shop/settings.py}

Another solution suggested by a friend is to look for "UNION" based queries in the mysql logs.
