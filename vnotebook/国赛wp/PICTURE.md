# PICTURE
binwalk跑图片可以看到是一个jpg，而不是文件名标注的png，而且可以看到有一个zlib的包，binwalk -e 可以解压出来，看到一段base64文本，解码之后是一个二进制文件，打开文件看到文件头KP，猜测是zip，修改为PK后可以打开为zip，根据注释里的提示，用python2.7进行除零操作，得到密码integer division or modulo by zero，解压之后，根据begin end判断是一个uuencode文件，decode即可得到flag
