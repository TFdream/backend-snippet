# 常用Linux命令

## curl
curl POST JSON：
```
curl -H "Accept: application/json" -H "Content-type: application/json" -X POST -d '{"id":2228359,"userId":5797485,"shopId":5,"memberId":41603,"changeValue":19799,"changeType":3,"businessId":"ES20211101000118308307","changeDirection":2,"changeComment":"积分兑换","createTime":"2021-11-01 00:01:18","beforeChange":56555,"afterChange":36756,"orderId":931670,"orderGoodsIds":"1388666"}' http://renzhen-smap-scheduler.user.renzhenmall.prod/task/shopping-ranking/buy

```

## mv命令
### 1、语法
```
mv [选项] 源文件或目录 目标文件或目录
```
选项如下：
* -b ：若需覆盖文件，则覆盖前先行备份。
* -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
* -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
* -u ：若目标文件已经存在，且 source 比较新，才会更新(update)
       
### 2、例子 
实例一：文件改名 
```
mv test.log test1.txt
```
实例二：移动文件 
```
mv test1.txt test3
```
实例三：将文件log1.txt,log2.txt,log3.txt移动到目录test3中 
```
mv log1.txt log2.txt log3.txt test3
```
实例四：将文件file1改名为file2，如果file2已经存在，则询问是否覆盖 
```
mv -i log1.txt log2.txt
```
实例五：将文件file1改名为file2，即使file2存在，也是直接覆盖掉 
```
mv -f log3.txt log2.txt
```
实例六：目录的移动 
```
mv dir1 dir2 
```
实例七：移动当前文件夹下的所有文件到上一级目录 
```
mv * ../
```
实例八：把当前目录的一个子目录里的文件移动到另一个子目录里 
```
mv test3/*.txt test5
```
实例九：文件被覆盖前做简单备份，前面加参数-b  
```
mv log1.txt -b log2.txt
```

