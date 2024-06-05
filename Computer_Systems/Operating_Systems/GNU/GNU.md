---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
## Definition
_GNU stands for GNU is Not Unix_
It is a **project** with the purpose of turning **Open-Source** [[UNIX|UNIX-like]] [[Operating System|Operating Systems]] into **Open-Source** Operating Systems by implementing **free-software** components such as libraries, packages, tools, games and applications. For example, the **grep** command, the **GCC compiler** (which stands for GNU Compiler Collection) or the **Bash shell** are examples of GNU utilities.

The term _GNU_ stands for _GNU is Not Unix_ because It differs from UNIX by being **free software**.
Therefore, GNU can be mixed in conjunction with a UNIX-like Operating System: e.g. **GNU/[[Linux]]**, providing the classic [[#Bash]] shell.
## GPG public and private keys
_GPG stands for GNU Privacy Guard_
It uses the **PGP Standard**.
## Bash
_Bash stands for Bourne Again SHell_
It is a [[Operating System#Shell|CLI Shell]] with its own shell-specific language. It is the classic shell you can find inside [[Linux]]:
![[Bash-shell.png]]
### Useful commands
Some commands that are provided by GNU Bash are the following. However, you can also run [[UNIX#UNIX-CLI standard commands]] (i.e. Bash is not limited to just GNU commands).
Every command is a binary file (located in [[Linux#/bin]] directory).
#### echo (Print text to the terminal)
```shell
echo "Hello, World!"
```
#### read (Read input from the user)
```shell
read -p "Enter your name: " <name>
```
##### Read some string and store it in Bash variable
If you run:
```shell
read store_variable
```
The Shell will let you write anything. What you write will be stored in ```store_variable```. Then, you can ```echo store_variable``` and see the content.
You can also run:
```shell
read store_variable <<< "example"
```
To store a constant value inside ```store_variable```.
##### String tokenization with read
Tokenize the string and store the tokens in some variable with name ```tokenized_string_variable_name```:
```shell
IFS=<delimiter_char> read -a tokenized_string_variable_name <<< <input_string>
```
With ```-a```, you specify the output to be an array.
Iterate over each token and print them:
```
for token in "${tokenized_string_variable_name[@]}"; do
> command to run
> done (to finish the loop)
```
For instance:
```shell
IFS=',' read tokenized_string_variable_name <<< "hello,goodby,hello"
```
After running the previous command:
```shell
for token in "${tokenized_string_variable_name[@]}"; do echo $token; done
```
#### grep (Search for patterns in files)

#### sed (Stream editor for text transformation)
#### find (Search for files in a directory hierarchy)
```shell
find <directory> -name "..."
```
Interesting flags:
- `-type f`: search for files
- `-type d`: search for directories
Example: find all files from the current directory with `.py` extension:
```shell
find . -type f -name "*.py"
```
#### chmod (Change file or directory permissions)
##### Specifying Octal Notation
```shell
sudo chmod <octal_number> <target_file_or_directory>
```
Where `octal_number` consists of three digits:
- The first digit represents permissions for the owner of the file [[Linux#File owner]].
- The second digit represents permissions for the [[Linux#Group owner]] that the file belongs to.
- The third digit represents permissions for **others** (users who are not the owner and not in the group).
The [[Linux#Permissions]] have a representing number:
- `r (read) = 4`
- `w (write) = 2`
- `x (execute) = 1`
Thus, each digit in `octal_number` is the sum of the values of the permissions we would like to grant.
For example, if we want to grant all permissions (`rwx`) for File owner but no permissions for the Group owner or others, then the value must be `700`, which means `(rwx) --- ---`.
#### chown (change ownership)
You can change the [[Linux#File owner]] with:
```shell
sudo chown <new_file_owner> <filename>
```
#### /bin/bash (execute a Bash Script)
```shell
/bin/bash <script-name.sh>
```
Or:
```shell
./<script-name.sh>
```
#### gawk: a GNU extension of AWK
#### gpg
To convert from ASCII OpenPGP public-private key in `.asc` format to `.gpg`. For example, input can be:
```
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQENBFI3HsoBCADXDtbNJnxbPqB1vDNtCsqhe49vFYsZN9IOZsZXgp7aHjh6CJBD
A+bGFOwyhbd7at35jQjWAw1O3cfYsKAmFy+Ar3LHCMkV3oZspJACTIgCrwnkic/9
CUliQe324qvObU2QRtP4Fl0zWcfb/S8UYzWXWIFuJqMvE9MaRY1bwUBvzoqavLGZ
j3SF1SPO+TB5QrHkrQHBsmX+Jda6d4Ylt8/t6CvMwgQNlrlzIO9WT+YN6zS+sqHd
1YK/aY5qhoLNhp9G/HxhcSVCkLq8SStj1ZZ1S9juBPoXV1ZWNbxFNGwOh/NYGldD
2kmBf3YgCqeLzHahsAEpvAm8TBa7Q9W21C8vABEBAAG0RUVsYXN0aWNzZWFyY2gg
KEVsYXN0aWNzZWFyY2ggU2lnbmluZyBLZXkpIDxkZXZfb3BzQGVsYXN0aWNzZWFy
Y2gub3JnPoasdEAAh4FAAoJENJ9ZmzYjkKdfdfdf2ssdfds23hoH+wYXZKgVb3Wv
4AA/+T1IAf7edwgajr58bEyqds6/4v6uZBneUaqahUqMXgLFRX5dBSrAS7bvE/jx
+BBQx+rpFGxSwvFegRevE1zAGVtpgkFQX0RpRcKSmksucSBxikR/dPn9XdJSEVa8
vPcs11V+2E5tq3LEP14zJL4MkJKQF0VJl5UUmKLS7U2F/IB5aXry9UWdMTnwNntX
kl2iDaViYF4MC6xTS24uLwNwe43543D2St0GEwbdBVvp+UZ/kGIGkYM5eWGPuok/
DHvjUdwTfyO9b5xGbqn5FJ3UFOwB/nOSFXHM8rsHRT/67gHcIl8YFqSQXpIkk9D3
dCY+KieW0ue5AQ0EUjceygEIAOSVJc3DFuf3LsmUfGpUmnCqoUm76Eqqm8xynFEG
ZpczTChkwARRtckcfa/sGv376sdsfdsw238Dxl3gpS6nHZ9Gsnfr/kcH9/11sdsH
Ca73HBtmGVIkOI1mZKMbANO8cewY/i7fPxShu7B0Rb3jxVNGUuiRcfRiao0gWx0U
ZGpvuHplt7loFX2cbsHFAp9WsjYEbSohb/Y0K4NkyFhL82MfbcsEwsXPhRTFgJWw
s4vpuFg/kFFlnw0NNPVP1jNJLNCsMBMEpP1A7k6MRpylNnUAEQEAAYkBNgQYAQgA
IAIbDBYhBEYJWsyFSFgsGiaZqdJ9ZmzYjkK0BQJk9vsHAAoJENJ9ZmzYjkK0hWsH
/ArKtn12HM3+41zYo9qO4rTri7+IYTjSB/JDTOusZgZLd/HCp1xQo4SI2Eur3Rtx
USMWK1LEeBzsjwDT9yVceYekrBEqUVyRMSVYj+UeZK2s4LbXm9b4jxXVtaivmkMA
jtznndrD7kmm8ak+UsZplf6p6uZS9TZ9hjwoMmw5oMaS6TZkLT4KYGWeyzHJSUBX
YikY6vssDQu4SJ07m1f4Hz81J39QOcHln5I5HTK8Rh/VUFcxNnGg9360g55wWpiF
eUTeMyoXpOtffiUhiOtbRYsmSYC0D4Fd5yJnO3n1pwnVVVsM7RAC22rc5j/Dw8dR
GIHikRcYWeXTYW7veewK5Ss=
=ftS0
-----END PGP PUBLIC KEY BLOCK-----

```

```shell
gpg --dearmor <ascii_file.asc>
```
Interesting flags:
- `-o`: to specify the path and filename of the `.gpg` output file


