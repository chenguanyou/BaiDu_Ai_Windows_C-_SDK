

<h1 style="background:#6AB942;color:#FFFFFF;padding:10px">windows下编译liburl的步骤【https】</h1>
<String style="color:red">windows系统版本 (可以略过，也可看下。)：</String>

```
因为我这里是使用的虚拟机，也是刚刚因为这个Windows系统下的C++ SDK安装的，所以一切都要重新安装，顺便把各种环境也
奉献给大家作为参考。
系统版本：windows10 Pro 64位
下载链接:https://pan.baidu.com/s/1Tt10MYfjXMJtEzzxURaKyw  密码:5aka
```

<String style="color:red">所用到的环境：</String>

```
1、vs
2、openssl
3、libcurl
```

<String style="color:red">一、安装VS (一定要有)</String>

```
其实这个版本是多少都可以，我随便安装的2017版本的免费版。
```
<String style="color:red">二、编译OpenSSL</String>

```
1、安装：ActivePerl
	找到安装的bin目录，把bin目录配置到环境变量。
	下载地址：http://www.activestate.com/activeperl/downloads

2、安装：Nasm汇编工具
	找到安装的bin目录，把bin目录配置到环境变量。
	下载地址：http://www.nasm.us/ 

3、OpenSSL和Curl下载
	OpenSSL下载地址：https://www.openssl.org/source/openssl-1.0.2e.tar.gz
	Curl下载地址：https://curl.haxx.se/download/curl-7.30.0.tar.gz
	把两个下载好的文件放在一个文件夹内。

4、先编译openssl打开命令提示符， 进入到目录openssl目录， 
	cd 解压后的openssl目录路径，执行命令：perl Configure VC-WIN32 --prefix=c:\opensslib，
	执行上面一部切记记得安装ActivePerl，并且配置环境变量。
	继续执行命令：ms\do_nasm，
	然后前往：C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin 这里根据自己的系统路径找
	然后输入：vcvars32.bat
	然后回到openssl目录，执行nmake -f ms\nt.mak
	完成以后当前目录会生成一个out32的目录，里面包含了生成的文件啦。
	然后输入nmake -f ms\nt.mak install，是为了将生成的库拷贝到c:\opensslib里面。
	完成后c:\opensslib目录里面会生成四个目录[bin, include, lib, ssl]。
	到此为止我们openssl就编译完啦。
```

<String style="color:red">三、编译Curl</String>

```
1、关于Curl我们之前在编译openssl的时候已经下载并且解压过了，我们直接进入解压好的curl-7.30.0\winbuild目录里。

2、如果想看一下编译命令，通过 nmake /f Makefile.vc 查看帮助
	
	如果openssl是静态库 执行 nmake /f Makefile.vc mode=dll VC=10 WITH_DEVEL=C:\opensslib 
	WITH_SSL=static ENABLE_SSPI=no ENABLE_IPV6=no
	
	
	如果openssl是动态库 执行 nmake /f Makefile.vc mode=dll VC=10 WITH_DEVEL=C:\opensslib 
	WITH_SSL=dll ENABLE_SSPI=no ENABLE_IPV6=no


	参数说明：
		mode=dll 编译libcurl位动态链接库，如果static 就是把libcurl编译位静态库 
		VC=10 代表使用的是VC2010
		WITH_DEVEL=C:\openssl_lib 表示用到第三方开发包的目录，本例上面已经将openssl编译好的开发包，安装到此目录
		WITH_SSL=static ,代表使用libssl库 是静态库，如果是动态库使用dll
		ENABLE_SSPI=no ENABLE_IPV6=no（禁用SSPI，ipV6功能）


3、测试刚刚编译出来的curl库是否支持https

	如果是使用的openssl静态库直接进行下面的测试，直接进入解压好的curl-7.30.0\builds\libcurl-vc10-x86-release-
	dll-ssl-static-spnego\bin
	
	然后输入命令：curl.exe https://mail.qq.com  -k
	
	如果使用的是openssl动态库需要把opensslib/lib/里面libeay32.dll和ssleay32.dll都拷贝到解压好的curl-7.30.0\builds\libcurl-vc10-x86-release-dll-ssl-dll-spnego\bin文件夹下。
	
	直接进入解压好的curl-7.30.0\builds\libcurl-vc10-x86-release-dll-ssl-dll-spnego\bin
	
	然后输入命令：curl.exe https://mail.qq.com  -k
	
	如果一切OK，那么恭喜你，支持https的那个libcurl也就安装完成了！
```