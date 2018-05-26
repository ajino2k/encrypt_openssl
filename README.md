# encrypt_openssl

# 1. Symmetic Encryption 
- Đối với mã hóa đối hóa thì bạn có thể sử dụng ví dụ sau cho việc mã hóa dữ liệu của bạn .
- Encrypt : 
 
      openssl aes-256-cbc -salt -a -e -in cuongnp2.zip -out encrypt_cuongnp2.zip
 
- Decrypt  :
 
      openssl aes-256-cbc -salt -a -d -in encrypt_cuongnp2.zip -out decrypt_cuongnp2.zip
 
# 2. Asymmetric encryption
 
- Trước tiên bạn phải tạo 1 private key và public key bằng command sau : 
 
      openssl genrsa -aes256 -out private.key 8912
      openssl rsa -in private.key -pubout -out public.key

- Encrypt :

      openssl rsautl -encrypt -pubin -inkey public.key -in cuongnp2.zip -out encrypt_cuongnp2.zip

- Decrypt :

      openssl rsautl -decrypt -inkey private.key -in encrypt_cuongnp2.zip -out decrypt_cuongnp2.zip

# 3. Encripting files

- Bạn không thể mã hóa 1 têp file lớn bằng rsautl , thay vào đó hãy sử dụng cách sau đây :
	* Tạo ra key sử dụng ```openssl rand``` , e.g. ```openssl r 32 -out keyfile```
	* Mã hóa keyfile ```openssl rsautl```.
	* Mã hóa dữ liệu : ```openssl```, sử dụng để tạo ra 1 key ở bước 1
	* Bạn đóng gói tệp khóa được mã hóa với dữ liệu  mã hóa. Người nhận sẽ chỉ cần giải mã khóa bằng khóa riêng của họ, sau đó giải mã dữ liệu bằng khóa kết quả
- Giải pháp tối ưu cho bất kỳ tệp mã hóa được bảo mật và an toàn cao nào trong OpenSSL và dòng lệnh:
- Tạo private key :

      openssl genrsa -aes256 -out private.pem 8912
      openssl rsa -in private.pem -pubout -out public.pem

-  Private key chưa được mã hóa : </br>
   ```openssl req -x509 -nodes -days 100000 -newkey rsa:8912 -keyout private_key.pem -out certificate.pem```

- Private key đã được mã hóa : </br>
   ```openssl req -x509 -days 18250 -newkey rsa:8912 -keyout private_key.pem -out certificate.pem```
- Với viêc mã hóa hiện tại sử dụng private key :</br>
   ```openssl req -x509 -new -days 100000 -key private_key.pem -out certificate.pem ```

# 4. Encrypt a file
- Encrypt  binary file </br>
   ```openssl smime -encrypt -binary -aes-256-cbc -in cuongnp2.zip -out encrypt_cuongnp2.zip.enc -outform DER yourSslCertificate.pem```

- Encrypt text file </br>
   ```openssl smime -encrypt -aes-256-cbc -in cuongnp2.txt -out ecrypt_cuongnp2.txt -outform DER yourSslCertificate.pem ```


### What is what:
* ghi chú mình để tiếng ENG cho nó chính xác nhé (^^):

```smime``` — ssl command for S/MIME utility (smime(1)). </br>
```-encrypt``` — chosen method for file process. </br>
```-binary``` — use safe file process. Normally the input message is converted to "canonical" format as required by the S/MIME specification, this switch disable it. It is necessary for all binary files (like a images, sounds, ZIP archives).</br>
```-aes-256-cbc``` — chosen cipher AES in 256 bit for encryption (strong). If not specified 40 bit RC2 is used (very weak). (Supported ciphers).</br>
```-in plainfile.zip``` — input file name. </br>
```-out encrypted.zip.enc``` — output file name.</br>
```-outform DER``` — encode output file as binary. If is not specified, file is encoded by base64 and file size will be increased by 30%. </br>
```yourSslCertificate.pem``` — file name of your certificate's. That should be in PEM format.

# 5. Decrypt a file

- Decrypt binary file :

```openssl smime -decrypt -binary -in encrypt_cuongnp2.zip.enc -inform DER -out decrypt_cuongnp2.zip -inkey private.key -passin pass:Aa123456```

- Decrypt text file :

```openssl smime -decrypt -in encrypted_cuongnp2.txt -inform DER -out decrypted_cuongnp2.zip -inkey private.key -passin pass:Aa123456```

What is what:
* ghi chú mình để tiếng ENG cho nó chính xác nhé (^^):

```-inform DER``` — same as -outform above. </br>
```-inkey private.key``` — file name of your private key. That should be in PEM format and can be encrypted by password.</br>
```-passin pass:your_password``` — (optional) your password for private key encrypt.</br>

# 6 . Verification

- Tạo signed degest cho file :

   ```openssl dgst -sha512 -sign private_key.pem -out digest.sha512 cuongnp2.txt```

- Xác minh quá trinh tạo signed degest :

   ```openssl dgst -sha512 -verify public_key.pem -signature digest.sha512 cuongnp2.txt```

# Nguồn : 
* http://bsdsupport.org/2007/01/q-how-do-i-use-openssl-to-encrypt-files/
* http://stackoverflow.com/questions/7143514/how-to-encrypt-a-large-file-in-openssl-using-public-key
* http://www.madboa.com/geek/openssl/
* http://stackoverflow.com/questions/5140425/openssl-command-line-to-verify-the-signature
* http://www.openssl.org/docs/apps/openssl.html#PASS_PHRASE_ARGUMENTS
