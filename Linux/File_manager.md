# Linux文件管理操作

## 磁盘管理命令

df: 查看系统中文件系统的空间占用情况
$ df -h

du ：统计目录或文件占用空间
$ du -h

free: 查看内存使用状态

fdisk：查看硬盘分区情况及对硬盘进行分区管理

## 文件系统操作

- stat命令列出元数据信息
- 建立文件touch 

## 查看文件内容

Linux提供了多个命令用来在显示屏上显示文本文件的内容。cat、tac、nl、pr、more 、head、tail等

-利用特殊字符“>”将名称为file1与 file2 的文件合并成一个文件file3：   
cat file1 file2 > file3

## 文件的复制、转移、删除和大小
文件的复制命令cp  
语法：cp [options] file1 file2

文件转移命令mv

文件删除命令rm

文件大小的确定  
    ls -l file-list

## 文件的压缩、排序、查找

reserved
