## 1.在linux工作目录创建qemu字目录，去[github](https://github.com/qemu/qemu) clone最新qemu 

## 2.在线安装必要的库
**_sudo apt-get install libglib2.0-dev_**

**_sudo apt-get install libpixman-1-dev_**

**_sudo apt-get install libfdt-dev_**

sudo apt-get install ninja-build

## 3.进入新克隆的qemu目录，新建build目录，进入build目录

## 4.运行配置执行指令
sudo ../configure 

注意最后的报错信息，可能会缺少flex,bison等程序

## 5.编译qemu（为了缩短编译时间，建议多核编译）
ps:慢点总比卡死好
make -j16（16核）

## 6.安装qemu
sudo make install

## 7.查看qemu安装信息
qemu-system-aarch64 -version

### 8.[QEMU启动](https://juejin.cn/post/7113721438380490766)（它是图形化启动的）
qemu-system-x86_64 -nographic test-vm-1.qcow2  无图形化界面启动