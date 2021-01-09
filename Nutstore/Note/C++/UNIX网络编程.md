# 第一章

## 笔记

TCP传输层的PDU特称为分节，分节除了用于承载应用数据外，也用于：

建立连接（SYN分界）、终止连接（FIN分节）、中止连接（RST分节）、确认数据接收（ACK分节）、刷送待发分节（PSH分节）、携带紧急数据指针（URG分节），各分节可以灵活组合

UDP将来自应用进程的单个记录封装在一个UDP消息中传送给接收端UDP。

SCTP引入了块（chunk）的数据单元，一个消息由一个公共首部加上一个或多个块构成。

sprintf无法检查目的缓冲区是否溢出，使用snprintf代替sprintf的好处是其第二个参数指定目的缓冲区的大小，可确保缓冲区不溢出。snprintf存在于C99。

许多网络入侵是由黑客通过发送数据，导致服务器对sprintf的调用使其缓冲区溢出而发生的。必须小心使用的函数还有gets、strcat和strcpy，通常应分别改为fgets、strncat和strncpy，更好的是之后引入的strlcat和strlcpy，它们确保结果是正确终止的字符串。 

```shell
netstat -ni	 	#-i提供网络接口的信息，-n标志以输出数值地址

docker0   1500        0      0      0 0             0      0      0      0 BMU
enp2s0f1  1500   838658      0      0 0        409744      0      0      0 BMRU
lo       65536     6103      0      0 0          6103      0      0      0 LRU

```

lo为环回（loop back），eth/enp为以太网接口

```shell
netstat -r		#展示路由表，也是另一种确定接口的方法。

~ netstat -nr
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.16.83.254   0.0.0.0         UG        0 0          0 enp2s0f1
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 enp2s0f1
172.16.82.0     0.0.0.0         255.255.254.0   U         0 0          0 enp2s0f1
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0

```

```shell
ifconfig enp2s0f1	#指定接口的详细信息
enp2s0f1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.82.188  netmask 255.255.254.0  broadcast 172.16.83.255
        inet6 fe80::b03d:1f27:cb56:d327  prefixlen 64  scopeid 0x20<link>
        ether 80:fa:5b:56:6e:31  txqueuelen 1000  (Ethernet)
        RX packets 866018  bytes 849667635 (849.6 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 425802  bytes 37725259 (37.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

**POSIX（可移植操作系统接口，Portable Operating System Interface）**

由电气与电子工程师学会IEEE开发的一系列标准

## 课后练习

按照提示首先在命令行执行 ./configure，没有问题。

执行 cd lib 进入lib文件夹，执行make命令，没有问题。

执行 cd …/libfree进入libfree文件夹，执行make命令，会遇到以下问题：

错误解决办法是：进入libfree文件夹，打开inet_ntop.c,在第60行将size_t size改为socklen_t size。

再执行第6步，问题解决。

在命令行执行 cd …/intro/，进入intro文件夹，

执行 make daytimetcpcli，生成可执行文件后，

执行 ./daytimetcpcli 127.0.0.1 会出现以下问题：

***Connection refused***
运行时间获取程序，需要现在ubuntu里安装时间服务

执行以下命令

```shell
sudo apt-get install xinetd
sudo vi /etc/xinetd.d/daytime
```

将的两个disable的值改为no:

执行 ***service xinetd restart***
重新执行./daytimetcpcli 127.0.0.1，成功

执行 ***service xinetd restart***
10.重新执行./daytimetcpcli 127.0.0.1，结果如图

**C语言中函数参数从右到左进行处理。出入栈操作**

# 第二章

## 笔记	

​	最小链路MTU（maximum transmission unit，最大传输单元）：

​		ipv4指定68字节，允许最大的ipv4首部（固定长度20+选项部分40）拼接最小的片段（60+8）

​		ipv6指定1280字节，可以运行在链路小于该值的情况下，不过必须分片重组。

​	ipv4主机对其产生的数据执行分片，ipv4路由器则对其转发的数据执行分片。

​	ipv6只有主机对其产生的数据报执行分片。

​	一个标记为ipv6路由器的设备可能执行分片，是对于它产生的数据报而言。当设备产生数据报时，它实际上作为主机在运行。

​	大多数的路由器支持Telent协议，由Telent服务器产生的IP数据报是由路由器产生的，而不是由路由器转发的。

​	ipv4 DF位为不分片位（don't fragment）。

​	许多防火墙会丢弃所有ICMP消息。

​	最小重组缓冲区大小(minimum reassembly buffer size)，任何实现都必须保证支持的最小数据报的大小。

​		对于ipv4为576字节，对于ipv6为1500字节

​	TCP最大分节大小（maximun segment size），用于告诉对端其重组缓冲区大小的实际值，试图避免分片。

​	MSS = 最小重组缓冲区大小（ipv4=576，ipv6=1500）-IP首部定长（ipv4=20 ，ipv6=40）-TCP首部定长（20）

​	UDP避免了TCP连接建立和终止所需的开销。

​	TIME_WAIT状态时长为两个最长分节生命周期（maximum segmemt lifetime，MSL），2MSL。

​	拥有最大跳限（hop limit，255）的分组在网络中存在的时间不可能超过MSL秒。

​	TIME_WAIT存在的理由：

​	（1）可靠地实现TCP全双工连接的终止；

​	（2）允许老的重复分节在网络中消逝。

​	Cookie 并不是它的原意“甜饼”的意思, 而是一个保存在客户机中的简单的文本文件, 这个文件与特定的 Web 文档关联在一起, 保存了该客户机访问这个Web 文档时的信息, 当客户机再次访问这个 Web 文档时这些信息可供该文档使用。由于“Cookie”具有可以保存在客户机上的神奇特性, 因此它可以帮助我们实现记录用户个人信息的功能, 而这一切都不必使用复杂的CGI等程序 。

​	举例来说, 一个 Web 站点可能会为每一个访问者产生一个唯一的ID, 然后以 Cookie 文件的形式保存在每个用户的机器上。如果使用浏览器访问 Web, 会看到所有保存在硬盘上的 Cookie。在这个文件夹里每一个文件都是一个由“名/值”对组成的文本文件,另外还有一个文件保存有所有对应的 Web 站点的信息。在这里的每个 Cookie 文件都是一个简单而又普通的文本文件。透过文件名, 就可以看到是哪个 Web 站点在机器上放置了Cookie(当然站点信息在文件里也有保存)    。

## 课后练习

1. IP版本0是保留的，版本1-3未曾分配，版本5是网际网流协议（Internet Stream Protocol）。

2. 在RFC1819文档定义了ipv5的第二版本。

4. 在一个以太网上的主机和一个令牌环网上的主机之间建立一个连接，其中以太网上主机的TCP通告的MSS为1460，令牌环网上主机的TCP通告MSS为4096。两个主机都没有实现路径MTU发现功能，在两个相反方向上都找不到大于1460字节的数据，为什么？

   答：令牌环网不会发送超过1460字节的数据，这是对端通告的，而以太网的主机可以发送4096字节的数据，但是为了避免分片，它不会超过外出接口（即以太网1500字节）的MTU，所以TCP的净荷一定小于1460。TCP净荷不能超过由对端宣告的MSS，但是净荷小于这个数量的TCP分节总是可以发送的。

6. 承载OSPF数据报的IPv4首部的协议字段是89。
7. SCTP选择性确认只是表明由选择性确认的消息反映的序列号所涵盖的数据已被接收，而累积缓冲区中基于选择性确认消息中的序列号指示的所有以前的数据都已被接收。如果从发送缓冲区中基于选择性确认释放数据，那么系统只能释放确切被确认的数据，而不能释放之前或之后的任何数据。

# 第三章

## 笔记



值-结果参数

以引用方式来传递，当函数被调用时，结构大小是一个值（value），它告诉内核该结构的大小这样内核在写该结构时不至于越界；当函数返回时，结构大小又是一个结果（result），它告诉进程内核在结构中究竟存储了多少信息。这种类型的参数称为值-结果（value-result）参数。



网络字节序和主机字节序互换

```c
#include<netinet/in.h>
uint16_t htons(uint16_t host16bitvalue);
uint32_t htonl(uint32_t host32bitvalue);	//均返回网络字节序的值

uint16_t ntohs(uint16_t net16bitvalue);
uint32_t ntohl(uint32_t net32bitvalue);		//均返回主机字节序的值
```

h代表host，n代表net，s表示一个16位的值，l表示一个32位的值。

字节操纵函数：Berkeley实现

```c
#include<string.h>
void bzero(void *dest,size_t nbytes);
void bcopy(const void *src,void *dest,size_t nbytes);
int bcmp(const void *ptr1,const void *ptr2,size_t nbytes);	//相等为0，否则非0
```

ANSI C实现

```c
#include<string.h>
void *memset(void *dest,int c,size_t len);
void *memcpy(const void *dest,void *src,size_t nbytes);
int memcmp(const void *ptr1,const void *ptr2,size_t nbytes);	//相等为0，ptr1>ptr2,大于0，反之小于0
```

dest = src（赋值）

ASCII字符串和网络字节序的二进制值相互转换函数

```c
#include<arpa/inet.h>

int inet_aton(const char *strptr,struct in_addr *addrptr); //若字符串有效返回1，否则为0
in_addr_t inet_addr(const char *strptr); //若字符串有效则返回32位二进制网络字节序的IPv4地址，否则为INADDR_NONE
char *inet_ntoa(struct in_addr inaddr);	//返回一个点分十进制数串的指针
```

表达（presentation）和数值（numeric）相互转换函数

```c
#include<arpa/inet.h>
int inet_pton(int family,const char *strptr,void *addrptr); //若成功则返回1，若输入不是有效的表达格式则返回0，出错返回-1
const char *inet_ntop(int family,const void *addrptr,char *strptr,size_t len);//若成功返回指向结果的指针，若出错则返回NULL
```

表达格式通常是ASCII字符串，数值格式则是存放到socket地址结构中的二进制值。



IPv4地址和TCP或DUP端口号在socket地址结构中总是以网络字节序来存储。

```c
#include <arpa/inet.h>
#include <string.h>
#include <errno.h>
#include <stdio.h>
//pton的简化实现，只支持ipv4
int inet_pton(int family, const char *strptr, void *addrptr){
	if(family == AF_INET){			//支持ipv4
		struct in_addr in_val;		//存放地址

		if(inet_aton(strptr,&in_val)){		//如果返回值为1，即字符串有效，将ASCII转换为二进制ipv4地址
			memcpy(addrptr , &in_val,sizeof(struct in_addr));	//将转换值复制到addrptr所指向的空间中
			return 1;						//返回1
		}
		return 0;							//如果字符串无效，返回0
	}
	errno = EAFNOSUPPORT;					//如果不是ipv4的地址族，errno设置为EAFNOSUPPORT，并返回-1
	return -1;
}

//ntop的简化实现，只支持ipv4
const char * inet_ntop(int family,const void *addrptr,char *strptr,size_t len){
	const u_char *p = (const u_char *) addrptr;		//将传入的二进制地址强制转换为无符号字符型
	if(family == AF_INET){				//支持ipv4
		char temp[INET_ADDRSTRLEN];		//一个大小为ipv4ASCII地址长度的数组。
		snprintf(temp,sizeof(temp),"%d.%d.%d.%d",p[0],p[1],p[2],p[3]);	//将转换后的地址输出，并拷贝至temp中

		if(strlen(temp) >=len ){	//若拷贝之后temp的长度大于len，则返回null，设置errorno为ENOSPC
			errno = ENOSPC;
			return NULL;
		}
		strcpy(strptr,temp);		//将temp拷贝至strptr并返回
		return strptr;
	}
	errno = EAFNOSUPPORT;			//若不是ipv4地址族则返回NULL，设置errno为EAFNOSUPPORT
	return NULL;
}
```

inet_ntop要求调用者知道地址结构的格式和地址族，这样函数就与协议相关了，可以自己编写一个与协议无关的函数，使用自己的静态缓冲区保存结果，指向缓冲区的指针为返回值。

```c
char * sock_ntop(const struct sockaddr *sa,socklen_t salen){
	char portstr[8];
	static char str[128];		//unix缓冲区最大为128字节

	switch (sa->sa_family){		//在函数内进行协议族的判断
		case AF_INET:{			//ipv4协议族
			struct sockaddr_in *sin = (struct sockaddr_in *) sa; 	//将sa强制转换为sockaddr_in，方便提取端口号和地址。

			if(inet_ntop(AF_INET,&sin->sin_addr , str,sizeof(str)) == NULL){	//如果ntop出错，返回NULL
				return NULL;
			}
			if(ntohs(sin->sin_port)!=0){		//如果端口号不为0，将端口号由网络字节序转换为主机字节序后，输出端口号并拷贝至portstr
				snprintf(portstr,sizeof(portstr),":%d",ntohs(sin->sin_port));
				strcat(str,portstr);			//从str中删除端口号
			}
			return str;			//返回ip地址
		}

		case AF_INET6:{		//ipv6协议族
			struct sockaddr_in *sin = (struct sockaddr_in *) sa;
			if(inet_ntop(AF_INET6,&sin->sin_addr , str,sizeof(str)) == NULL){	//如果ntop出错，返回NULL
				return NULL;
			}
			if(ntohs(sin->sin_port)!=0){		//如果端口号不为0，将端口号由网络字节序转换为主机字节序后，输出端口号并拷贝至portstr
				snprintf(portstr,sizeof(portstr),":%d",ntohs(sin->sin_port));
				strcat(str,portstr);			//从str中删除端口号
			}
			return str;			//返回ip地址
		}
	}

	return NULL; 
}
```

read或write输入或输出的字节数可能比请求的数量少，原因可能是内核中用于socket的缓冲区可能已经达到了极限。此时所需的是调用者再次调用read或write函数。这个现象在读一个字节流socket时很常见，但是在写一个字节流socket时只能在该socket为非阻塞的时候才出现。

总是使用writen来取代weite。

readn函数：

```c
#include<string.h>
#include<stdio.h>
#include<errno.h>
ssize_t readn(int fd, void *vptr, size_t n){
    size_t nleft;       //sockfd中剩余的字符,fd = file descriptor
    size_t nread;       //读取的字节
    char *ptr;          //读取位置的指针

    ptr = vptr;
    nleft = n;
    while(nleft>0){     //当字节还有剩余
        if((nread = read(fd,ptr,nleft))<0){     //如果read错误，判断是程序中断还是其他原因     
            if(errno == EINTR){                 //如果进程在一个慢系统调用中阻塞时，nread设置为0
                nread = 0;
            }
            else{                               //若为其他原因，返回-1；
                return -1;
            }
        }
        else if (nread == 0){                   //等于0代表读取完毕，跳出循环
            break;
        }

        nleft -= nread;                         //从剩余字节中减掉读取的字节
        ptr +=nread;                            //将指针向后移动至读取的字节之后
    }
    return (n-nleft);                           //返回最终读取的字节数
}
```

writen函数：

```c
ssize_t writen(int fd,const void *vptr,size_t n){
    size_t nleft;       //剩余字节
    ssize_t nwritten;   //写入的字节
    const char *ptr;    //位置指针

    ptr = vptr;
    nleft =n;
    while(nleft >0){
        if((nwritten = write(fd,ptr,nleft)) <= 0){
            if(nwritten <0 && errno == EINTR){
                nwritten = 0;
            }
            else{
                return -1;
            }
        }

        nleft -= nwritten;
        ptr += nwritten;
    }
    return n;
}
```

readline函数

```c
ssize_t readline(int fd,void *vptr,size_t maxlen){
    ssize_t n,rc;
    char c,*ptr;

    ptr = vptr;
    for(n=1;n<maxlen;n++){
        if((rc = read(fd,&c,1)) == 1){  //如果成功读取，进入循环
            *ptr++ = c;
            if(c == '\n'){              //遇到换行符终止循环
                break;
            }
            else if(rc == 0){           //如果读取结束并没有遇到换行符，返回读取的所有字节的个数。
                *ptr = 0;
                return n-1;             
            }
            else{
                if(errno == EINTR){     //如果中断，尝试重新读取
                    n--;
                    continue;
                }
                return -1;
            }
        }
    }
    *ptr = 0;
    return n;                           //返回最终读取的字节数
}
```

readline的效率十分地下，因为每使用一次read只读取一个字节。但是相比于stdio来说危险性小，在编写网络程序时，最好是根据缓冲区来编程，而不是根据文本行来编程，stdio虽然性能高，但是其缓冲区是不可见的，这对网络应用来说是一种隐患。

radline函数改造版，提高了效率而且不使用stdio。

```c
#define MAXLINE 4096
static int read_cnt;
static char *read_ptr;
static char read_buf[MAXLINE];

static ssize_t my_read(int fd, char *ptr){
    if(read_cnt <= 0){
    again:
        if((read_cnt = read(fd,read_buf,sizeof(read_buf)))<0){
            if(errno == EINTR){
                goto again;
            }
            return -1;
        }else if(read_cnt == 0){
            return 0;
        }
        read_ptr = read_buf;
    }
    read_cnt--;
    *ptr = *read_ptr++;
    return 1;
}

ssize_t readline(int fd,void *vptr,size_t maxlen){
    ssize_t n,rc;
    char c,*ptr;

    ptr = vptr;
    for(n=1;n<maxlen;n++){
        if((rc = my_read(fd,&c)) == 1){  //如果成功读取，进入循环
            *ptr++ = c;
            if(c == '\n'){              //遇到换行符终止循环
                break;
            }
            else if(rc == 0){           //如果读取结束并没有遇到换行符，返回读取的所有字节的个数。
                *ptr = 0;
                return n-1;             
            }
            else{
                if(errno == EINTR){     //如果中断，尝试重新读取
                    n--;
                    continue;
                }
                return -1;
            }
        }
    }
    *ptr = 0;
    return n;                           //返回最终读取的字节数
}

ssize_t readlinebuf(void **vptrptr){
    if(read_cnt){
        *vptrptr = read_ptr;
    }
    return read_cnt;
}
```

内部函数my_read每次最多读MAXLINE个字符，每次返回一个字符。

readline函数唯一变化是用my_read调用取代read

readlinebuf这个新函数能够展露内部缓冲区的状态，便于调用者查看在当前文本行之后是否受到了新的数据。

但是使用静态变量实现跨相继函数调用的状态信息维护，其结果是这些函数变得不可重入或者说非线程安全。

## 课后练习

1. 为什么诸如socket地址结构的长度之类的值-结果参数要用指针来传递？

   因为C函数无法通过传入一个变量从而改变这个变量，只能传入地址来改变这个地址中的内容。

2. 为什么readn和writen函数都将void \*型指针转换为char\*型指针?

   void*类型不能进行算法操作，进行算法操作的指针必须是确定知道其指向数据类型大小的，函数中使用了自增操作。

3. 试写一个名为inet_pton_loose 的函数，如果地址族为AF_INET，调用inet_pton 返回值为0，则调用inet_aton。成功返回一个IPv4的地址，类似的若地址族为AF_INET6 ，返回一个IPv4映射的IPv6地址。

```c
#include <string.h>
#include <stdio.h>
#include <sys/socket.h>
#include <unistd.h>
#include <errno.h>
#include <arpa/inet.h>

int inet_pton_loose(int family, const char *strptr, void *addrptr){
	if(family == AF_INET){			//支持ipv4
		struct in_addr in_val;		//存放地址
        int a;
        if((a=inet_pton(family,strptr,addrptr)) ==0){
            printf("inet_pton failed");
            if(inet_aton(strptr,&in_val)){		//如果返回值为1，即字符串有效，将ASCII转换为二进制ipv4地址
			    memcpy(addrptr , &in_val,sizeof(struct in_addr));	//将转换值复制到addrptr所指向的空间中
			    return 1;						//返回1
		    }
		    return 0;							//如果字符串无效，返回0
        }
        return 1;
	}

    if(family ==AF_INET6){
        struct in_addr in_val;
        int a;
        if((a=inet_pton(family,strptr,addrptr))==0){
            printf("inet_pton failed");
            if(inet_aton(strptr,&in_val)){
                memcpy(addrptr,&in_val,sizeof(struct in_addr));
                return 1;
            }
            return 0;
        }
        return 1;
    }
}

int main(int argc, char **argv){
    if(argc<3){
        printf("input format: ./main <family> <address>\n");
        return 1;
    }
    in_addr_t addr;;
    bzero(&addr, sizeof(addr));

    if(strcmp(argv[1],"AF_INET")==0){
        if(inet_pton_loose(AF_INET,argv[2], (void*)&addr) <=0){
            printf("error");
            return 0;
        }
        printf("IPv4 address: %08x\n ",addr);
        return 1;
    }
    if(strcmp(argv[1],"AF_INET6")==0){
        if(inet_pton_loose(AF_INET6,argv[2], (void*)&addr) <=0){
            printf("error");
            return 0;
        }
        printf("IPv6 address:%08x\n",addr);
        return 1;
    }
	printf("family not support\n");
    return 0;
}}
```

按照我理解的提议，该程序为pton的进化版，因为pton的要求严格，所以当pton调用不成功时，调用aton来进行转换，这样可以保证转换的成功率。

**该代码存在问题**：当地址族为AF_INET6时，输入的ip地址会出现\*** stack smashing detected \***: terminated，原因尚不明确，暂时搁置。

# 第四章

## 笔记

一个进程首先要做的就是调用socket函数，指定通信协议类型。

```c
#include<sys/socket.h>
int socket(int family,int type,int protocol); //若成功则为非负描述符，若出错则为-1
```

TCP仅支持SOCK_STREAM

Linux支持一个新的socket类型SOCK_PACKET，支持对数据链路的访问。

| family   | 说明       |
| -------- | ---------- |
| AF_INET  | IPv4协议   |
| AF_INET6 | IPv6协议   |
| AF_LOCAL | Unix域协议 |
| AF_ROUTE | 路由socket |
| AF_KEY   | 密钥socket |

| type   | 说明       |
| -------- | ---------- |
| SOCK_STREAM | 字节流socket |
| SOCK_DGRAM | 数据报socket |
| SOCK_SEQPACKET | 有序分组socket |
|   SOCK_RAW  | 原始socket  |

| protocol   | 说明       |
| -------- | ---------- |
| IPPROTO_TCP  | TCP传输协议   |
| IPPROTO_UDP | UDP传输协议   |
| IPPROTO_SCTP | SCTP传输协议 |

AF\_前缀表示地址族，PF\_前缀表示协议族

connect函数

```c
#include<sys/socket.h>
int connect(int sockfd,const struct sockaddr *servaddr,socklen_t addrlen); //成功返回0，出错返回-1
```

sockfd是socket函数返回的socket描述符，第二、三个参数分别是一个指向socket地址结构的指针和该结构的大小。

产生RST的三个条件：

1. 指定的但口没有正在监听的服务器
2. TCP想取消一个已有连接
3. TCP接收到一个根本不存在的连接上的分节

connect是一次性的，若是失败则该sockfd不再可用，必须关闭，并新建一个sockfd再进行connect。

bind函数

```c
#include<sys/socket.h>
int bind(int sockfd,const struct sockaddr *servaddr,socklen_t addrlen); //成功返回0，出错返回-1
```

如果未使用bind绑定一个端口，则调用connect和listen时，内核就要为socket绑定一个临时端口，这对TCP客户端是正常的，但对服务端来说很少见。例外是远程调用服务器（RPC）。

| IP地址 | 端口 | 结果 |
| ---- | ---- |      |
| 通配地址 | 0 | 内核选择IP地址和端口         |
| 通配地址 | 非0 | 内核选择IP地址，进程选择端口 |
| 本地IP地址 | 0 | 进程指定IP地址，内核选择端口 |
| 本地IP地址 | 非0 | 进程指定IP地址和端口 |

若指定通配地址，则当发送数据之后内 核才会进行IP地址的选择。

listen函数

```c
#include<sys/socket.h>
int listen(int sockfd,int backlog); //成功返回0，出错返回-1
```

关于backlog，有很多的解释以及实现。**不要把backlog设置为0。**

未完成的连接队列处于SYN_RCVD状态，已完成的连接队列处于ESTABLISHED状态，两队列之和不超过backlog(历史上)。

未完成的连接队列中任何一项在其中的存留时间是一个RTT，RTT的取值决定于特定的客户与服务器。

通过复写环境变量LISTENQ来解决backlog的大小问题

```c
#include<sys/socket.h>
#include<stdlib.h>
#include<stdio.h>
void err_sys(const char * str) 
{ 
    fprintf(stderr,"%s\n",str); //stderr标准错误，不带缓冲，可以尽快将错误输出	stdio.h
    exit(1); 
}
void Listen(int fd,int backlog){  
    char *ptr;
    if((ptr = getenv("LISTENQ"))!=NULL){	//getenv获取环境变量值	stdlib.h
        backlog = atoi(ptr);		//atoi函数将ascii转换为integer，即将字符串转换成数字
    }

    if(listen(fd,backlog) < 0){
        err_sys("listen error");
    }
}
```

指定较大backlog值的理由在于：随着客户SYN分节的到达，未完成连接队列中的项数可能增长，他们等着三路握手的完成。

当一个客户SYN到达时，若这些队列是满的，TCP就会忽略该分节，也就是不发送RST。这只是暂时的，之后TCP会使用重传机制重新发送SYN，直到找到一个可用空间或者服务端发送一个RST。

客户端**无法分辨**接收到的RST是因为**该端口没有服务器在监听**还是**因为服务器队列满**。

SYN泛滥（SYN flooding）攻击，以高速率给受害者主机发送SYN，填满未完成连接队列，并将每个SYN发送源地址设为随机数，防止暴露真正源地址。

accept函数

```c
#include<sys/socket.h>
int accept(int sockfd,struct sockdaar *cliaddr,socklen_t *addrlen); //若成功则返回非负描述符，若出错则返回-1
```

参数cliaddr和addrlen用来返回已连接的对端进程（客户）的协议地址。addrlen是值-结果参数，调用前，将由*addrlen所引用的整数值置为由cliaddr所指的socket地址结构的长度，返回时，该整数值即为由内和存放在该socket地址结构内的确切字数。

第一个参数为监听socket（listening socket），由socket创建，随后用于bind和listen的第一个参数的描述符。返回值为一个已连接socket（connected socket）。

例子：值-结果参数

```c
//package.h
#include<sys/socket.h>
int Socket(int family,int type,int protocol);
void Bind(int fd,const struct sockaddr *sa,socklen_t len);
void Listen(int fd,int backlog);
int Accept(int fd,const struct sockaddr *sa,socklen_t *salenptr);
void Write(int fd, void *ptr, size_t nbytes);
void Close(int fd);

//package.c
#include "package.h"
#include "error.h"
#include <stdio.h>
#include <sys/socket.h>
#include <stdlib.h>
#include <errno.h>
#include <unistd.h>
int Socket(int family,int type,int protocol){
    int n = 0;
    if((n=socket(family,type,protocol))<0){
        err_sys("socket error");
    }
    return n;
}

void Bind(int fd,const struct sockaddr *sa,socklen_t len){
    if(bind(fd,sa,len)<0){
        err_sys("bind error");
    }
}
void Listen(int fd,int backlog){
    char *ptr;
    if((ptr=getenv("LISTENQ"))!=NULL){
        backlog = atoi(ptr);
    }
    if(listen(fd,backlog)<0){
        err_sys("listen error");
    }
}
int Accept(int fd,const struct sockaddr *sa,socklen_t *salenptr){
    int n;\
again:
    if((n=accept(fd,&sa,salenptr))<0){
#ifdef  EPROTO
        if(errno ==EPROTO || errno == ECONNABORTED)
#else
        if(errno == ECONNABORTED)
#endif
            goto again;
        else
        err_sys("accept error");
    }
    return n;
}
void Write(int fd ,void *ptr,size_t nbytes ){
    if(write(fd,ptr,nbytes)!=nbytes){
        err_sys("write error");
    }
}
void Close(int fd){
    if(close(fd)==-1){
        err_sys("close error");
    }
}
```

```c
//error.h
void err_sys(const char * str);

//error.c
#include<stdlib.h>
#include<stdio.h>
#include"error.h"
void err_sys(const char * str) 
{ 
    fprintf(stderr,"%s\n",str); 
    exit(1); 
}
```

```c
//server1.c
#include<time.h>
#include<sys/socket.h>
#include<stdio.h>
#include<netinet/in.h>
#include<string.h>
#include<arpa/inet.h>
#include"package.h"
#define MAXLINE 4096
#define LISTENQ 1024
#define	SA	struct sockaddr
int main(int argc,char **argv){
    int listenfd,connfd;
    socklen_t len;
    struct sockaddr_in servaddr,cliaddr;
    char buff[MAXLINE];
    time_t ticks;
    listenfd = Socket(AF_INET,SOCK_STREAM,0);
    bzero(&servaddr, sizeof(servaddr));
    servaddr.sin_family = AF_INET;
    servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
    servaddr.sin_port = htons(5000);

    Bind(listenfd,(SA*)&servaddr,sizeof(servaddr));
    
    Listen(listenfd,LISTENQ);

    for(;;){
        len = sizeof(cliaddr);
        connfd = Accept(listenfd,(SA *)&servaddr,len);
        printf("connection from %s, port %d\n",
        inet_ntop(AF_INET,&cliaddr,buff,sizeof(buff)),
        ntohs(cliaddr.sin_port));
        ticks = time(NULL);
        snprintf(buff,sizeof(buff),"%.24s\r\n",ctime(ticks));
        Write(connfd,buff,strlen(buff));

        Close(connfd);
    }
    return 0;
}
```

调用时原本是13端口，但是使用root进行运行发现端口已被占用，所以更换为5000端口，不需要使用root，能达到一样的教育效果。

fork函数，UNIX中派生新进程的唯一方法。

```c
#include<unistd.h>
pid_t fork(void);	//在子进程中返回0，在父进程中返回子进程ID，若出错则为-1。
```

该函数调用一次却返回两次，返回值告知当前进程是子进程还是父进程。

子进程可以通过调用getppid()获取父进程的ID，但是父进程无法获取每个子进程的ID，只能通过fork函数的返回值实现。

fork的典型用法：
（1）一个进程创建一个自身的副本，每个副本都可以在另一个副本执行其他任务的同时处理各自的某个操作。这是网络服务器的典型用法。

（2）一个进程想要执行另一个程序。

存放在硬盘上的可执行程序文件能够被Unix执行的唯一方法是：由一个现有进程调用六个exec中的其中一个。**进程ID并不改变。**我们称调用exec的进程为调用进程（calling process），称新执行的程序为新程序（new program）。

6个exec函数之间的区别在于：（a）待执行的程序文件是由文件名（filename）还是由路径名（pathname）指定；

​												 （b）新程序的参数是一一列出还是由一个指针数组来引用；

​												 （c）把调用进程的环境传递给新程序还是给新程序指定新的环境。

```c
#include<unistd.h>

int execl(const char *pathname, const char *arg0,.../*(char *) 0*/);
int execv(const char *pathname,char *const *argv[]);
int execle(const char *pathname.const char *arg0,.../*(char *) 0 ,char *const envp[]*/);
int execve(const char *pathname.char *const argv[],char *const envp[]);
int execlp(const char *filename, const char *arg0,.../*(char *) 0*/);
int execvp(const char *filename, char *const argv[]);
//若成功则不返回，若出错则为1。
```

若不出错，控制将被传递给新程序的起始点，通常就是main函数。

一般来说，只有execve是内核中的系统调用，其他5个都是调用execve的库函数。

带l的函数将新程序的参数都指定成一个独立的参数，并有一个独立的空指针参数，而带v的函数都是一个参数数组，既然没有指定参数大小，则参数数组中必须有一个用于结尾的空指针。

lp和lv结尾的函数指定了一个filename参数，exec将使用当前的PATH环境变量目录将其转换成路径名pathname，然而一旦这两个函数中有一个的filename中有一个斜杠（/），就不再使用PATH环境变量。其他四个函数指定了一个全限定的pathname函数。

不显式指定环境指针的函数使用外部环境变量environ的当前值来构造一个传递给新程序的环境列表。显式指定环境列表的函数，指针数组必须以一个空指针结束。

进程在调用exec之前打开着的描述符**通常**跨exec继续保持打开。可以使用fcntl设置FD_CLOEXEC描述符指令禁止掉。

每个文件或socket都有一个引用计数。引用计数在文件列表中维护，它是当前打开着的引用该文件或socket的描述符的个数。fork返回会将父进程的描述符引用计数与子进程共享，即复制一份，即原本引用计数为1的socket会变成2。

socket真正清理和资源释放要等到其引用计数值达到0时才会发生。 

close函数，关闭socket并终止TCP连接。

```c
#include<unistd.h>
int close(int sockfd);
```

