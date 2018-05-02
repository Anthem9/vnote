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

```
b64_tbl = Str + 35;
sub_140001554(77, Str + 35, 64u);
__int64 __fastcall sub_140001554(int a1, char *table_encoded, unsigned int length)
{
  __int64 v3; // kr00_8
  __int64 result; // rax
  signed int i; // [rsp+0h] [rbp-E8h]
  signed int j; // [rsp+4h] [rbp-E4h]
  int v7; // [rsp+8h] [rbp-E0h]
  int *v8; // [rsp+10h] [rbp-D8h]
  int v9; // [rsp+F0h] [rbp+8h]

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
    result = length;
    if ( j >= (signed int)length )
      break;
    if ( v7 == 50 )
      v7 = 0;
    table_encoded[j] ^= *((_BYTE *)&v8 + 4 * v7++);
  }
  return result;
}
```

这个函数对传递进来的数据做了一次变换，最终用变换过后的table来做base64的表，于是根据这个函数，联想到之前拿到的一串奇怪的数据，把那串数据送给这个函数一试，真的就解密成了一个表！表长下面这个样子：

`vwxrstuopq34567ABCDEFGHIJyz012PQRSTKLMNOZabcdUVWXYefghijklmn89+/`

然后字节写一个base64解密，把表替换掉，解密sys2中出现的字符串就可以得到flag了：

`90705bb55efb59da7fc2a5636549812a`

下来之后一定要好好分析这道题的反调试，看到了一些奇怪的技巧，尤其是指令混淆那个地方感觉很厉害，感觉还有好多东西没有分析出来就误打误撞撞出了flag，感谢出题人，学习了很多姿势。