---
title: 常见加密、信息摘要算法和Python使用
categories:
 - 加密
tags:
 - 加密
date: 2019-12-20 17:00:00
author: 刘旺
---


## 一、加密  

### 1、 加密算法的概念

就是以某种特殊的算法改变原有的信息数据，使得未授权的用户即使获得了已加密的信息，但因不知解密的方法，仍然无法了解信息的内容。

### 2、 如何进行加密

其实加密就是将明文信息隐匿起来,让其在缺少特殊信息时变为不可读状态

> **举个例子**:上课时,小明给小红传了一张纸条,上面写着`I LOVE YOU`,如果被老师捉到的话,很容易就被老师发觉两个人的地下工作,但是使用加密的话(用 J 代替I,K代替L,Z代替O,I代替V,F代替E,N代替Y,V代替U),即便被老师发现的话,那他只是拿到了一串无用的字符,即: `JKZIFNZV`.这样,便能让小明和小红的秘密不被发现,从而达到了加密的作用.

### 3、 加密如何保证安全

加密之所以安全,并不是因为不知道加密和解密的算法,而是因为加密的密钥是隐藏的.现如今比较流行的RSA和AES加密算法都是公开的,一方取得已加密的数据,就算知道加密算法,如果没有加密的密钥,也不能打开或者解读被加密保护的信息.

## 二、列举几种常见的加密算法和Python的应用

### 1、 前言

我们所说的加密方式，都是对二进制编码的格式进行加密的，对应到Python中，则是我们的`Bytes`。

所以当我们在Python中进行加密操作的时候,要确保我们操作的是`Bytes`,否则就会报错

### 2、 encode()和decode()

将字符串和`Bytes`互相转换可以使用`encode()`和`decode()`方法:

```python
a = "临江仙"
a.encode()
```

**输出结果**:`b'\xe4\xb8\xb4\xe6\xb1\x9f\xe4\xbb\x99'`

```python
a = b'\xe4\xb8\xb4\xe6\xb1\x9f\xe4\xbb\x99'
a.decode()	
```

**输出结果**:`'临江仙'`

### 3、 几种常用的加密算法

#### 3.1 DES(对称性加密算法)

**DES**算法为密码体制中的对称密码体制,是一个分组加密算法，典型的DES以64位为分组对数据加密，加密和解密用的是同一个算法。


**密钥**长64位，密钥事实上是56位参与DES运算（第8、16、24、32、40、48、56、64位是校验位，使得每个密钥都有奇数个1），分组后的明文组和56位的密钥按位替代或交换的方法形成密文组。

#### 3.1.2 DES块加密的两种模式：

1.电子密码本（ECB）：
    
原理：将数据包分成很多64bit的块，每块使用相同算法相同秘钥加密；

特点：相同的明文总是产生相同的密文；易受到插入，重放，字典攻击；

2. 加密块连接（CBC）：

原理：数据包被等分成很多64bit的块，并且发包时发送一个明文IV初始化向量位，向量位和第一个块做异或运算，然后用秘钥和算法加密得到密文，拿第一个密文和第二个明文做异或，得到第二个密文，这样持续下去……

特点：思科的IPSec VPN只支持cbc模式；有一个明文的IV初始化向量位；

#### 3.1.3  Python使用DES加密

```python
# 导入DES模块
import binascii
from pyDes import des, ECB, PAD_PKCS5
s = 'Beijing'
KEY = '12345678909876543'
k = des('        ', ECB)
k.setKey(KEY)
s = k.encrypt(s, padmode=PAD_PKCS5)
print(s)
data = binascii.b2a_base64(s)
print(data)
```

**输出结果**:`C3MWEU6hNwQ=`

> **注**:在初始化des对象时,如果密钥(即KEY)的长度不等于8,那么会抛出`Invalid DES key size. Key must be exactly 8 bytes long.`的异常

#### 3.1.4 Python使用DES解密

```python
# 导入DES模块
from pyDes import des, ECB, PAD_PACS5
s = 'C3MWEU6hNwQ='
KEY = '12345678909876543'
k = des("        ", ECB)
k.setKey(KEY)
s = k.encrypt(s)
print s
```

**输出结果**: `Beijing` 

> **问题**: 在使用过程中,通过查阅资料了解到,DES仍旧有较大的风险被破解,那么是否有比DES安全性更高的对称加密算法呢?
>
> **答**: 答案是有的,那就是3DES（即Triple DES）,它是DES向AES过渡的加密算法，它使用3条56位的密钥对数据    	  进行三次加密。是DES的一个更安全的变形。它以DES为基本模块，通过组合分组方法设计出分组加密算    	  法。比起最初的DES，3DES更为安全。
>
> ​      该方法使用两个密钥，执行三次DES算法，加密的过程是加密-解密-加密，解密的过程是解密-加密-解密。
### 3.2 RSA加密
**RSA加密算法**是一种非对称加密算法。在公开密钥加密和电子商业中RSA被广泛使用。

该算法基于一个十分简单的数论事实：将两个大素数相乘十分容易，但那时想要对其乘积进行因式分解却极其困难，因此可以将乘积公开作为加密密钥，即公钥，而两个大素数组合成私钥。公钥是可发布的供任何人使用，私钥则为自己所有，供解密之用。
#### 3.2.1 非对称加密
典型的如RSA等，常见方法，使用openssl ,keytools等工具生成一对公私钥对，使用被公钥加密的数据可以使用私钥来解密，反之亦然（被私钥加密的数据也可以被公钥解密) 。

在实际使用中私钥一般保存在发布者手中，是私有的不对外公开的，只将公钥对外公布，就能实现只有私钥的持有者才能将数据解密的方法。   这种加密方式安全系数很高，因为它不用将解密的密钥进行传递，从而没有密钥在传递过程中被截获的风险，而破解密文几乎又是不可能的。

但是算法的效率低，所以常用于很重要数据的加密，常和对称配合使用，使用非对称加密的密钥去加密对称加密的密钥。

> **注**: 我们经常用的git的SSHKey,就用到了RSA这种加密方法.

#### 3.2.2. Python实现RSA加密
```python
from Crypto import Random
from Crypto.PublicKey import RSA
# 伪随机数生成器
random_generator = Random.new().read
# rsa算法生成实例,目前来看使用1024依旧有被破解的奉献,所以最好使用2048.
rsa = RSA.generate(1024, random_generator)
# master的秘钥对的生成
private_pem = rsa.exportKey()
# --------------------------------------------生成公私钥对文件---------------------------------
# 私钥
print private_pem
print '====================='*20
public_pem = rsa.publickey().exportKey()
# 公钥
print public_pem
```

运行结果：**public_pem公钥**
```RSA
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC/jGHVW9ns1DgGdliFdQDtaG1b
xncAaHZ5r0a9cZGapjSgcWvy1Q0/fuu0fMTwKPUu3mapsjkfoMGvlvQ6F32QGhji
/SQAgHJfVaWqKMyJKD1sN4rLB+8eUtIRBxUxdSdCzlTJn59LGJvl0l2I1DY7omyy
1X/fJNoeS3Z4yspdWwIDAQAB
-----END PUBLIC KEY-----
```

运行结果:  **private_pem私钥**

```
-----BEGIN RSA PRIVATE KEY-----
MIICXAIBAAKBgQC/jGHVW9ns1DgGdliFdQDtaG1bxncAaHZ5r0a9cZGapjSgcWvy
1Q0/fuu0fMTwKPUu3mapsjkfoMGvlvQ6F32QGhji/SQAgHJfVaWqKMyJKD1sN4rL
B+8eUtIRBxUxdSdCzlTJn59LGJvl0l2I1DY7omyy1X/fJNoeS3Z4yspdWwIDAQAB
AoGAYQ5JmCk1T+nsBWI70kuJz8GE/+ac76j+twOX7W+5HkovCkjOMLnBP7YYylga
FbI7IadxD3UNurfizAzJ7RFv/g43RMrSbx7KDlOtEzLfHdyKt41aODQNhJ5Fgcfz
VrPEbfA/CCm/PoTr3h1ygHgEPtJCfZYfILPssJ9ZHXcoM9ECQQDOi75JdauE2TZ+
J5JYoPYw0t/veozFIrwI2rj/Xcf9o0UdrpT6YQGNuvP/vbzbGRiBAkRpjUNgFV+u
XPN4QZiZAkEA7WldZQ+Fw9gG/h7O3Hq6CB4cQCfSSJUainXl4kmK+l7vXmpNRzQA
SclqckrzI798pWUoz3fbH2Ew9N0LHSCaEwJAK0sdWraAejsxceYHhe5PTFKXP9I3
OYcbVNvL1TMqHV8guKUEcyrsMXITfPKfaGltOnVoM5u5mKVCRgETw4w0WQJBAKDS
zlSGZlvbfCkq34CVlm2nUAXZs+Y9MwR2hyp6HS4rn0U7B2xjeoqBaZ1QzlH6BkrY
MwQPsh0YrpxBHKU3j4cCQCRpEb2itaneyYxKiAieMdFdYVBmluOgBtZfiCgOx1Ej
B1xOY7mZR0CeXuYCU931iqJHMNt30q0KYmM6LpiQOpw=
-----END RSA PRIVATE KEY-----
```

#### 3.2.3 Python实现RSA解密

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5 as Cipher_pkcs1_v1_5
import base64


message = 'hello ghost, this is a plian text'
rsakey = RSA.importKey(public_pem)  # 导入读取到的公钥
cipher = Cipher_pkcs1_v1_5.new(rsakey)  # 生成对象
# 通过生成的对象加密message明文，注意，在python3中加密的数据必须是bytes类型的数据，不能是str类型的数据
cipher_text = base64.b64encode(cipher.encrypt(message.encode(encoding="utf-8")))
rsakey = RSA.importKey(private_pem)  # 导入读取到的私钥
cipher = Cipher_pkcs1_v1_5.new(rsakey)  # 生成对象
# 将密文解密成明文，返回的是一个bytes类型数据，需要自己转换成str
text = cipher.decrypt(base64.b64decode(cipher_text), "ERROR")  
print(text)
```

运行结果: **解密后的明文信息**

```
hello ghost, this is a plian text
```

## 四、信息-摘要算法

### 4.1  信息-摘要算法的概念 

信息摘要算法的主要特征是加密过程不需要密钥，并且经过加密的数据无法被解密，目前可以被解密逆向的只有CRC32算法，只有输入相同的明文数据经过相同的信息摘要算法才能得到相同的密文。

###  4.2  信息-摘要算法共有的特点

1. 无论输入的消息有多长，计算出来的消息摘要的长度总是固定的.

2. 消息摘要看起来是“随机的”.

3. 一般地，只要输入的消息不同，对其进行摘要以后产生的摘要消息也必不相同；但相同的输入必会产生相同的输出。这正是好的消息摘要算法所具有的性质：输入改变了，输出也就改变了；两条相似的消息的摘要确不相近，甚至会大相径庭。

4. 消息摘要函数是无陷门的单向函数，即只能进行正向的信息摘要，而无法从摘要中恢复出任何的消息，甚至根本就找不到任何与原信息相关的信息.

   > **陷门**: 计算机操作的陷门设置是指进入程序的秘密入口,它使得知道陷门的人可以不经过通常的安全检查访问过程而获得访问。程序员为了进行调试和测试程序,已经合法地使用了很多年的陷门技术。当陷门被无所顾忌的程序员用来获得非授权访问时,陷门就变成了威胁。对陷门进行操作系统的控制是困难的,必须将安全测量集中在程序开发和软件更新的行为上才能更好地避免这类攻击。

5. 好的摘要算法，没有人能从中找到“碰撞”，虽然“碰撞”是肯定存在的。即对于给定的一个摘要，不可能找到一条信息使其摘要正好是给定的。或者说，无法找到两条消息，使它们的摘要相同。

### 4.3 MD5(信息-摘要算法)

**MD5**可以将一个字符串，或文件，或压缩包，执行md5后，就可以生成一个固定长度为128bit的串。这个串，基本上是唯一的.MD5是不可逆的

> **疑问1**:MD5码不是128位的吗?为何得出来的乱码有的是32位的，有的是16位的?
>
> 1. 128位是指二进制位。二进制太长，所以一般都改写成16进制，每一位16进制数可以代替4位二进制数,所以128位二进制数写成16进制就变成了128/4=32位。
> 2. 其实16位的长度，是从32位md5值来的。是将32位md5去掉前八位，去掉后八位得到的。

#### 4.3.1 MD5的特点

1. 压缩性：任意长度的数据，算出的MD5值长度都是固定的。
2. 容易计算：从原数据计算出MD5值很容易。
3. 抗修改性：对原数据进行任何改动，哪怕只修改1个字节，所得到的MD5值都有很大区别。
4. 强抗碰撞：已知原数据和其MD5值，想找到一个具有相同MD5值的数据（即伪造数据）是非常困难的。
5. 不可逆性：每个人都有不同的指纹，看到这个人，可以得出他的指纹等信息，并且唯一对应，但你只看一个指纹，是不可能看到或读到这个人的长相或身份等信息。

#### 4.3.2 Python使用MD5

```python
from hashlib import md5

zfc = "xxx"
cd = md5(zfc.encode("utf-8")).hexdigest()
print(cd) 
```

**输出结果**:`f561aaf6ef0bf14d4208bb46a4ccb3ad`

>**思考**：在什么样业务场景下选用哪种加密方式？
