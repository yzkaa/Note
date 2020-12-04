# day 1

 发现以下问题：

1. 想做一个登录界面，登录界面为子窗口，当把主窗口hide()之后发现不管用，主窗口和子窗口依然同时出现。
2. 向QLabel中添加图片后，发现图片无法按照原始比例缩放，导致图片被拉伸，非常难看。

解决以下问题：

1. 解决了主窗口不隐藏的问题：将main.cpp中的w.show()删除即可，w为MainWindow的对象，在主函数进行声明以及调用。删除后MainWindow依旧在后台运行，在函数中调用w.show()即可。
2. 解决了向QLabel中添加图片后无法按照原始比例缩放的问题：在ui设计界面将Label的水平策略调整为“Ignored”，然后手动调整Label的尺寸即可。

学到了以下知识点：

1. 使用QPalette为窗口元素进行上色

   ```c++
       //color
       QPalette pa =this->palette();   //palette object
       pa.setColor(QPalette::Background,qRgb(255,255,255));//white
       this->setAutoFillBackground(true);
       this->setPalette(pa);
   
   ```

   QPalette为调色板类。具体查看文档。

2. 使用QPixmap获取图片，并插入到Label中

   ```c++
    QPixmap *usr = new QPixmap(":/image/user.png");  //get png
       usr->scaled(l_ui->label_usr->size(),Qt::KeepAspectRatio,Qt::SmoothTransformation);//按比例进行填充
       l_ui->label_usr->setScaledContents(true);
       l_ui->label_usr->setPixmap(*usr);
   ```


3. 未解决问题：无

# day 2

发现以下问题：

1. Qt没有封装好的官方ssh库。
2. 第三方QSsh库使用问题。

解决以下问题：

1. 使用github上的第三方QSsh库，添加外部库即可。

2. QSsh库基本使用方法：

   ```c++
   /*.h文件*/
   #include<QSsh>
   QSsh::SshConnection *m_connection=nullptr;  //ssh socket连接，用来连接
   QSsh::SshConnectionParameters m_parameters; //一个对象，里面包含着地址，
   											//用户名，密码等
   void ssh_Init();							//用于ssh的初始化
   void ssh_Connect();							//用于ssh的连接
   void ssh_HandleConnected();					//连接成功后执行的操作
   void ssh_ConnectError(QSsh::SshError);		//链接失败后执行的操作
   
   /*.cpp文件*/
   /*ssh初始化实现*/
   void loginDialog::ssh_Init(){
       m_parameters.host = l_ui->lineEdit_ip->text();/*获取ip地址*/
       m_parameters.port = 22;/*获取端口号*/
       m_parameters.userName = l_ui->lineEdit_usr->text();/*获取用户名*/
       m_parameters.password = l_ui->lineEdit_pwd->text();/*获取密码*/
       m_parameters.timeout = 10;/*设置timeout*/
       //使用密码方式登录
       m_parameters.authenticationType = 							                   QSsh::SshConnectionParameters::AuthenticationTypePassword;
   }
   
   /*ssh连接函数实现*/
   void loginDialog::ssh_Connect(){
       ssh_Init();/*调用初始化函数，获取Label文本*/
   	/*new一个连接对象，必须传入参数，建议使用局部变量创建*/
       m_connection = new QSsh::SshConnection(m_parameters);
       /*连接对象中有一个connected信号，当连接成功时发出。将此信号与连接成功后的*/
       /*操作函数连接*/
       connect(m_connection,&QSsh::SshConnection::connected,
               			this,&loginDialog::ssh_HandleConnected);
       /*连接失败时，会发出error信号，将其与链接失败后操作函数相连*/
       connect(m_connection,&QSsh::SshConnection::error,
               			this,&loginDialog::ssh_ConnectError);
       /*启动连接*/
       m_connection->connectToHost();
   }
   /*连接成功后操作函数*/
   void loginDialog::ssh_HandleConnected(){
       l_ui->lable_ConnectInfo->setText("Connected");
       this->hide();//隐藏登录窗口
       emit access();//发出信号，主窗口收到信号后弹出
   }
   /*连接失败后操作函数，因为error有一个QSsh::SshError形参，所以在这加上*/
   void loginDialog::ssh_ConnectError(QSsh::SshError){
       l_ui->lable_ConnectInfo->setText("Connect Error!");
   }
   ```

未解决问题：无

# day 3

今日过于繁忙，鸽了。

# day 4

今日配置主机端的ftp服务器以及MySQL数据库，结果是理想的，都配置成功了。

主机系统：Ubuntu

配置时出现以及解决以下问题：

1. MySQL使用 

   ```shell
   sudo apt install mysql-server
   ```

   时发现安装后无法成功启动服务器，后来在网上查找到原来是需要在MySQL官网下载一个mysql-apt-config的依赖包，下载完成之后安装，然后进行更新：

   ```shell
   sudo apt update
   ```

   将下载MySQL依赖的源仓库更新到apt中，

   再次安装mysql-server时内容多了许多，一顿配置之后完成配置，用root用户进入无需密码。

2. ftp的配置

   按照网上教程配置

   （1）下载vsftpd

   ```shell
   sudo apt install vsftpd
   ```

   （2）安装完成后启动vsftpd服务，并查看启动状态

   ``` shell
   sudo service vsftpd start #启动
   sudo service vsftpd status #查看运行状态，Active为正常
   ```

   （3）添加用户并设置密码

   ```shell
   sudo useradd xxx -m #添加用户同时新建一个该用户的同名文件夹
   sudo passwd xxx #回车之后输入两次密码 
   ```

   （4）新建文件/etc/vsftpd.user_list，用于存放允许访问的用户

   ```shell
   sudo vim /etc/vsftpd.user_list
   
   #vsftpd.user_list文件内
   xxx
   ```

   （5）编辑配置文件（建议第一步就进行配置）

   以下是完整的配置文件

   ```shell
   #Example config file /etc/vsftpd.conf
   #
   # The default compiled in settings are fairly paranoid. This sample file
   # loosens things up a bit, to make the ftp daemon more usable.
   # Please see vsftpd.conf.5 for all compiled in defaults.
   #
   # READ THIS: This example file is NOT an exhaustive list of vsftpd options.
   # Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
   # capabilities.
   #
   #
   # Run standalone?  vsftpd can run either from an inetd or as a standalone
   # daemon started from an initscript.
   listen=NO  #ipv4监听
   #
   # This directive enables listening on IPv6 sockets. By default, listening
   # on the IPv6 "any" address (::) will accept connections from both IPv6
   # and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
   # sockets. If you want that (perhaps because you want to listen on specific
   # addresses) then you must run two copies of vsftpd with two configuration
   # files.
   listen_ipv6=YES		#ipv6监听
   #
   # Allow anonymous FTP? (Disabled by default).
   anonymous_enable=NO		#允许匿名用户访问
   #
   # Uncomment this to allow local users to log in.
   local_enable=YES		#允许本地用户访问
   #
   # Uncomment this to enable any form of FTP write command.
   write_enable=YES		#允许上传操作
   #
   # Default umask for local users is 077. You may wish to change this to 022,
   # if your users expect that (022 is used by most other ftpd's)
   #local_umask=022
   #
   # Uncomment this to allow the anonymous FTP user to upload files. This only
   # has an effect if the above global write enable is activated. Also, you will
   # obviously need to create a directory writable by the FTP user.
   #anon_upload_enable=YES
   #
   # Uncomment this if you want the anonymous FTP user to be able to create
   # new directories.
   anon_mkdir_write_enable=YES		#允许匿名用户创建文件夹（关闭匿名用户这个怎么设置都行）
   #
   # Activate directory messages - messages given to remote users when they
   # go into a certain directory.
   dirmessage_enable=YES			#用户访问文件夹信息
   #
   # If enabled, vsftpd will display directory listings with the time
   # in  your  local  time  zone.  The default is to display GMT. The
   # times returned by the MDTM FTP command are also affected by this
   # option.
   use_localtime=YES			#使用本地时间
   #
   # Activate logging of uploads/downloads.
   xferlog_enable=YES			#监听用户的上传和下载活动
   #
   # Make sure PORT transfer connections originate from port 20 (ftp-data).
   connect_from_port_20=YES		#主动连接模式 （port模式）
   pasv_enable=NO					#被动链接模式					
   #
   # If you want, you can arrange for uploaded anonymous files to be owned by
   # a different user. Note! Using "root" for uploaded files is not
   # recommended!
   #chown_uploads=YES
   #chown_username=whoever
   #
   # You may override where the log file goes if you like. The default is shown
   # below.
   #xferlog_file=/var/log/vsftpd.log
   #
   # If you want, you can have your log file in standard ftpd xferlog format.
   # Note that the default log file location is /var/log/xferlog in this case.
   #xferlog_std_format=YES
   #
   # You may change the default value for timing out an idle session.
   #idle_session_timeout=600
   #
   # You may change the default value for timing out a data connection.
   #data_connection_timeout=120
   #
   # It is recommended that you define on your system a unique user which the
   # ftp server can use as a totally isolated and unprivileged user.
   #nopriv_user=ftpsecure
   #
   # Enable this and the server will recognise asynchronous ABOR requests. Not
   # recommended for security (the code is non-trivial). Not enabling it,
   # however, may confuse older FTP clients.
   #async_abor_enable=YES
   #
   # By default the server will pretend to allow ASCII mode but in fact ignore
   # the request. Turn on the below options to have the server actually do ASCII
   # mangling on files when in ASCII mode.
   # Beware that on some FTP servers, ASCII support allows a denial of service
   # attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
   # predicted this attack and has always been safe, reporting the size of the
   # raw file.
   # ASCII mangling is a horrible feature of the protocol.
   #ascii_upload_enable=YES
   #ascii_download_enable=YES
   #
   # You may fully customise the login banner string:
   #ftpd_banner=Welcome to blah FTP service.
   #
   # You may specify a file of disallowed anonymous e-mail addresses. Apparently
   # useful for combatting certain DoS attacks.
   #deny_email_enable=YES
   # (default follows)
   #banned_email_file=/etc/vsftpd.banned_emails
   #
   # You may restrict local users to their home directories.  See the FAQ for
   # the possible risks in this before using chroot_local_user or
   # chroot_list_enable below.
   #chroot_local_user=YES
   #
   # You may specify an explicit list of local users to chroot() to their home
   # directory. If chroot_local_user is YES, then this list becomes a list of
   # users to NOT chroot().
   # (Warning! chroot'ing can be very dangerous. If using chroot, make sure that
   # the user does not have write access to the top level directory within the
   # chroot)
   #chroot_local_user=YES
   #chroot_list_enable=YES
   # (default follows)
   #chroot_list_file=/etc/vsftpd.chroot_list
   #
   # You may activate the "-R" option to the builtin ls. This is disabled by
   # default to avoid remote users being able to cause excessive I/O on large
   # sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
   # the presence of the "-R" option, so there is a strong case for enabling it.
   #ls_recurse_enable=YES
   #
   # Customization
   #
   # Some of vsftpd's settings don't fit the filesystem layout by
   # default.
   #
   # This option should be the name of a directory which is empty.  Also, the
   # directory should not be writable by the ftp user. This directory is used
   # as a secure chroot() jail at times vsftpd does not require filesystem
   # access.
   secure_chroot_dir=/var/run/vsftpd/empty #这个选项必须指定一个空的资料夹且任何登入者都不能有写入的权限，当vsftpd不需要file system 的权限时，就会将使用者限制在此资料夹中
   #
   # This string is the name of the PAM service vsftpd will use.
   pam_service_name=vsftpd #pam服务名称，默认使用配置文件/etc/pam.d/vsftpd
   #
   # This option specifies the location of the RSA certificate to use for SSL
   # encrypted connections.
   rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
   rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
   ssl_enable=NO		#启用ssl
   
   #
   # Uncomment this to indicate that vsftpd use a utf8 filesystem.
   utf8_filesystem=NO #使用utf-8编码进行转码，如果是为了windows配置的服务器建议关闭，因为Windows默认是gbk编码，使用utf-8会乱码，
   #userlist:
   userlist_file=/etc/vsftpd.user_list #之前添加的用户表
   userlist_enable=YES 	#启用用户表
   userlist_deny=NO		#该选项如果是YES，之前的用户表就会变成banner表，即在表上的用户不得登录
   
   
   ```

   ​	配置完成后重启vsftp服务即可

   （7）测试

   ​	在Windows或者Linux上测试该ftp服务器是否可连接

   ​	Windows上在资源管理器/浏览器/cmd上

   ```
   ftp://域名	#资源管理器/浏览器
   ftp 域名		#cmd
   ```

   ​	输入账号密码即可登录

   ​	Linux上还需下载ftp

   ​	

   ```shell
   sudo apt install ftp
   ```

   连接方式和cmd一致。



未解决问题：

​	ftp添加多个用户之后出现无法登录问题，经测试只有在Windows的资源管理器上无法登录，其他地方正常，而在资源管理器上需要以以下方式登录才可以

```
ftp://用户名:密码@域名
```

且输入用户名之后还需要再次选择用户登录，原因尚未得知。

# day 5

今日写Python题目，鸽了

# day 6

没有什么实质性进展，修改了服务器端的MySQL远程连接权限以及发现了几个小问题

发现问题：

1. MySQL配置问题
2. 客户端编写问题

解决问题：

​	1.MySQL在配置端口33066开放后，重启发现重启失败，经检查是因为配置文件最后一行没有空行导致。

2. 关于MySQL用户允许远程访问的问题。首先检查防火墙是否关闭，然后使用root账户进入MySQL，输入

   ```mysql
   GRANT ALL PRIVILEGES ON *.* TO 'kevin'@'192.168.101.234' 
   #*.*表示所有数据库下所有的表，左库右表，@前是用户名，@后是域名，如果想让该用户在任何域名下登录，将域名换成%即可。
   ```

未解决问题：

1. day4的历史遗留问题，经讨论后决定统一用‘ftp://用户名:密码@域名’格式进行登录。
2. 客户端编写问题，QtCreator自带Mysql库，但是没有编译，而Linux中的Qt文件不全，导致编译MySQL会提示undefine，所以决定下载Qt源码进行编译然后放入Qt中。现已下载源码，等待明天再进行编译

学机器学习去咯

# day 7

今日没什么实质性进展。主要研究与MySQL数据库的连接操作。

遇到的问题：

1. 很多，最大的问题就是打开数据库时提示QMYSQL:driver not loaded

2. 解决问题后出现ssl链接错误：unknown error

解决问题:

1. Qt MySQL驱动未加载问题。

   ​	这个问题真的很恶心，网上找了很多教程，整理一波，不得不说，网上的教程要凑一起看才有用。

   ​	首先看看自己Qt目录下xxx/Qt5.14.2/5.14.2/gcc_64/plugins/sqldrivers里面有没有一个叫libsqlmysql.so的库（这一步没必要，连不上的八成没有）

   ​	然后需要去下载MySQL压缩包，也可以安装，我选择下载压缩包，版本与服务器上数据库版本一致（重要！！！），解压缩之后进入目录，里面有个lib，lib里有几个libmysqlclient.so.*的文件，主角就是它们或者其中一个。把这些库复制到/usr/lib/x86_64-linux-gnu中

   ```shell
   sudo cp libmysqlclient.so.* /usr/lib/x86_64-linux-gnu
   ```

   ​	为什么复制到这里呢，等会就明白了

   ​	然后进入一个很深的文件夹--Qt中mysql源文件夹

   ​	先进入sqldriver文件夹

   ```shell
   cd xxx/Qt5.14.2/5.14.2/Src/qtbase/src/plugins/sqldrivers
   ```

   ​	修改qsqldriverbase.pri

   ```c++
   QT  = core core-private sql-private
   
   # For QMAKE_USE in the parent projects.
   #include($$shadowed($$PWD)/qtsqldrivers-config.pri) //注释掉
   
   PLUGIN_TYPE = sqldrivers
   load(qt_plugin)
   
   DEFINES += QT_NO_CAST_TO_ASCII QT_NO_CAST_FROM_ASCII
   
   ```

   ​	然后进入mysql文件夹

   ```shell
   cd mysql
   ```

   首先编辑mysql.pro

   ```c++
   TARGET = qsqlmysql
   
   HEADERS += $$PWD/qsql_mysql_p.h
   SOURCES += $$PWD/qsql_mysql.cpp $$PWD/main.cpp
   
   #QMAKE_USE += mysql		//注释掉这一句
   
   OTHER_FILES += mysql.json
   
   PLUGIN_CLASS_NAME = QMYSQLDriverPlugin
   include(../qsqldriverbase.pri)
   //以下为新添加内容
   //INCLUDEPATH添加刚才下载的MySQL里面的include路径
   INCLUDEPATH+=/home/yzk/Downloads/mysql-8.0.22-linux-glibc2.12-x86_64/include/
   //LIBS添加刚才下载的MySQL里面的lib里面的.so文件（我也不知道该用哪个，就随便加了一个）
   LIBS+=/home/yzk/Downloads/mysql-8.0.22-linux-glibc2.12-x86_64/lib/libmysqlclient.so.21.1.22
   //这是make之后文件生成目录，我直接放在自己的家目录下，方便找
   DESTDIR=/home/yzk/
   
   ```

   修改好之后执行qmake，这里要注意，首先看看自己的全局变量qmake是什么版本的

   ```shell
   qmake -v
   ```

   在这里就有个坑，如果和Qt版本不一样的话就会报错，有一些应用可能会修改qmake路径，比如anaconda。

   建议使用Qt自带的qmake进行编译，保证不会错。

   ```shell
   /xxx/Qt5.14.2/5.14.2/gcc_64/bin/qmake
   ```

   正常情况下是不会错的，生成makefile之后，执行make。这里注意，如果DESTDIR路径下有同名文件，make是不会进行的。

   将生成的.so文件复制到第一步的xxx/Qt5.14.2/5.14.2/gcc_64/plugins/sqldrivers中，然后查看它的依赖

   ```shell
    ldd libqsqlmysql.so
    	#以上省略
   	#重点就是这个.21,如果在之前将库文件拷贝到/usr/lib/x86_64-linux-gnu里的话，这里就是not found，然后啥都不会发生，网上没有这方面的解答，为啥要复制到这个文件夹里面呢，因为其他的依赖文件夹都在这里面，我就照着这个路径复制过去，没想到真的成功了
   	libmysqlclient.so.21 => /lib/x86_64-linux-gnu/libmysqlclient.so.21 (0x00007f124e30a000)
   	libQt5Sql.so.5 => /lib/x86_64-linux-gnu/libQt5Sql.so.5 (0x00007f124e2bf000)
   	libQt5Core.so.5 => /lib/x86_64-linux-gnu/libQt5Core.so.5 (0x00007f124ddc4000)
   	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f124dda3000)
   	libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f124dc1f000)
   	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f124da9c000)
   	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f124da80000)
   	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f124d8bf000)
   	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f124d8ba000)
   	libcrypto.so.1.1 => /lib/x86_64-linux-gnu/libcrypto.so.1.1 (0x00007f124d5a3000)
   	libssl.so.1.1 => /lib/x86_64-linux-gnu/libssl.so.1.1 (0x00007f124d50e000)
   	libresolv.so.2 => /lib/x86_64-linux-gnu/libresolv.so.2 (0x00007f124d4f4000)
   	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f124d4e8000)
   	/lib64/ld-linux-x86-64.so.2 (0x00007f124ec48000)
   	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f124d4cb000)
   	libicui18n.so.63 => /lib/x86_64-linux-gnu/libicui18n.so.63 (0x00007f124d1fd000)
   	libicuuc.so.63 => /lib/x86_64-linux-gnu/libicuuc.so.63 (0x00007f124d032000)
   	libpcre2-16.so.0 => /lib/x86_64-linux-gnu/libpcre2-16.so.0 (0x00007f124cfb7000)
   	libdouble-conversion.so.1 => /lib/x86_64-linux-gnu/libdouble-conversion.so.1 (0x00007f124cfa0000)
   	libglib-2.0.so.0 => /lib/x86_64-linux-gnu/libglib-2.0.so.0 (0x00007f124ce7f000)
   	libicudata.so.63 => /lib/x86_64-linux-gnu/libicudata.so.63 (0x00007f124b48f000)
   	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f124b41b000)
   
   ```

   

   检查依赖没有not found之后，驱动就算是打上了，回到程序。注意，在这里还有一个坑，要把你的工程文件里的xxx.pro_user文件删除，不然编译还是会提示driver not loaded。然后进入程序debug，没有提示driver not loaded了，但当我进行连接的时候，出现了ssl connection error。

   

   2. 解决ssl connect error：unknown error

      原因很简单，我出现这个问题的原因就是版本不正确。服务器上的数据库是8.0.22的，我下的是6.0.11的，就是这个问题困扰我半小时，再次重新下载并执行上述操作后，终于连接成功，问题解决。以上的配置文件都是最后一次正确配置的文件。

之后的计划：进行ftp文件和MySQL数据的显示，以及读写。

# day 8

学习，鸽了。

# dayN

....之前出了点问题，以至于鸽了好几天，忘记是第几天了，从明天开始继续记录。

# dayN+1

鸽了



# dayN+N

长久鸽，不知道什么时候会回来