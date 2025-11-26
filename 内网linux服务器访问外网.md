# 内网linux服务器访问外网



## 1.先查看本机IP地址

在桌面进入cmd 输入 `ipconfig` 查看本机IP地址

![image-20251119103541754](C:\Users\29800\AppData\Roaming\Typora\typora-user-images\image-20251119103541754.png)

记下这里的IP地址(如10.xx.xx.xx)



## 2.打开你自己的代理软件

设置如下（下面是两个不同的代理软件）

clash

<img src="C:\Users\29800\AppData\Roaming\Typora\typora-user-images\image-20251119104737968.png" alt="image-20251119104737968" style="zoom:50%;" />

FLclash

<img src="C:\Users\29800\AppData\Roaming\Typora\typora-user-images\image-20251119104809738.png" alt="image-20251119104809738" style="zoom:50%;" />

<img src="C:\Users\29800\AppData\Roaming\Typora\typora-user-images\image-20251119104909636.png" alt="image-20251119104909636" style="zoom: 50%;" />



## 3.进入Linux配置文件

- 运行指令
  `vim ~/.bashrc`
  在最后添加以下内容：

  ```
  export http_proxy=http://<proxy_host>:<proxy_port>
  export https_proxy=http://<proxy_host>:<proxy_port>
  ```

  这里<proxy_host>是你前面记下的主机IP地址，<proxy_port>是前面代理软件中的端口号，一般为7890
  <img src="https://img2024.cnblogs.com/blog/3555048/202503/3555048-20250325203641705-604145530.png" alt="img" style="zoom: 200%;" />
  这里如果不会vim编辑保存指令，请自行网上搜索(按i 进入编辑模式)
  编辑完成之后，保存并退出(按ESC 退出编辑模式；键盘输入`:wq`再按enter)
  最后为了使服务器修改后的文件生效，在终端执行命令：
  
  ```
  source ~/.bashrc
  ```
  
  
  
  

## 4.尝试访问外网
  在服务器终端输入

```
curl https://scholar.google.com/
```

成功访问外网，实现了服务器通过本地代理访问外网的目标
![img](https://img2024.cnblogs.com/blog/3555048/202503/3555048-20250325162257318-1442898807.png)