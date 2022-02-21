# Password Protector

Author :@yalatr3ch3

CTF: alphaCTF 2022

Category : Reverse Engineering

---
The challenge is about retrieving a secret from a password protected android app.

Using apktool we decode the apk, from a wild guess, I searched for the keyword flutter, found several flutter files, which means indeed the app was written in flutter.



```bash
$ apktool d com.alphactf.passwordprotector.apk
$ find . -name "*flutter*"

./com.alphactf.passwordprotector/lib/arm64-v8a/libflutter.so
./com.alphactf.passwordprotector/lib/x86/libflutter.so
./com.alphactf.passwordprotector/lib/x86_64/libflutter.so
./com.alphactf.passwordprotector/smali/io/flutter
./com.alphactf.passwordprotector/assets/flutter_assets

```

Not being a flutter developper, i googled around it a little bit.

* Flutter uses two modes when compiling, a debug mode and a release mode.

* In the debug mode, flutter keeps all the source code along with some comments in a file named kernel_blob.bin.

Let's see if we have that file.

```bash
$ find . -name kernel_blob.bin
./assets/flutter_assets/kernel_blob.bin
```

Bingo, lets go through the file.

```bash
$ cd ./assets/flutter_assets/
$ file kernel kernel_blob.bin
data
$ strings kernel kernel_blob.bin > code.txt
```

The file contains binary data, thats why we stringed it into a txt file, to have an easier outlook of the code.

Then it took me a while to find something meaningful, ( I am not familiar with dart ), after searching for some obvious keywords, like password, function, flag, void...

Going through the methods and voids i finally found this :

```dart
void initState() {
    fl4g = '';
    for (int i = 0; i < by73L157.length; i++) {
      fl4g = fl4g + String.fromCharCode(by73L157[i] ^ 1337);
    }
```

The `by73L157` list was just above it.

``` 
List<int> by73L157 = [1400,1365,1353,1361,1368,1402,1389,1407,1346,1367,1290,1359,1290,1355,1382,1292,1361,1293,1355,1290,1382,1293,1367,1382,1293,1353,1353,1382,1288,1367,1382,1373,1290,1371,1356,1280,1382,1364,1289,1373,1290,1348];
```

The code is easy to understand, its xoring each item of the list with 1337, and retrieving the character corresponding to that charcode, let's do that in python.

```python

a = [1400,1365,1353,1361,1368,1402,1389,1407,1346,1367,1290,1359,1290,1355,1382,1292,1361,1293,1355,1290,1382,1293,1367,1382,1293,1353,1353,1382,1288,1367,1382,1373,1290,1371,1356,1280,1382,1364,1289,1373,1290,1348];

flag=""
for i in range(len(a)):
    flag+=(chr((a[i]^1337))) 
print(flag)
# AlphaCTF{n3v3r_5h4r3_4n_4pp_1n_d3bu9_m0d3}

```

// Indeed, never share your app in debug mode!


