---
layout: post
title: 基于迅为4412精英版连接SIM7100C模块Android下调试——RIL库分析（二）
date: 2016-10-23
categories: 4G
tags: [Android,RIL]
description: about sim7100c study

---

1.Android电话系统设计框架图：
		  Android的智能机架构是应用处理器+基带芯片，也就是AP+Modem的模式，AP部分相当于CPU，Modem相当于网卡，而且每个厂商使用的Modem都有可能不一样,故Android 开发者使用的Modem 是不一样的，各种指令格式，初始化序列都可能不一样，所以为了消除这些差别，Android 设计者将ril 做了一个抽象，使用一个虚拟电话的概念，不同modem相关的AT指令或者通信协议编译成相应的动态链接库.so文件，Rild 是具体的AT 指令合成者和应答解析者。
		
![这里写图片描述](http://img.blog.csdn.net/20161014090018453)
_____
Android电话系统代码结构:
![这里写图片描述](http://img.blog.csdn.net/20161016150027456)
*
2.互相之间的通信
		RILC与上层的RILJ沟通方式是通过Socket传输数据与命令，而与底层Modem的信号传输是通过串口用AT命令来实现。
		
![这里写图片描述](http://img.blog.csdn.net/20161017163950135)

3.ril-daemon的启动
		ril-daemon进程是由init进程在系统开机时负责启动的，该进程在我们系统启动之后就一直存在在系统里面了。
在init.rc(.../out/target/product/sabresd_6dq/root/init.rc对应源码  .../system/core/rootdir/init.rc)中可以看到如下代码：

![这里写图片描述](http://img.blog.csdn.net/20161016152719157)

ril-daemon守护进程指的是system/bin/下的可执行程序rild，而rild是由.../hardware/ril/rild/目录下的rild.c文件编译生成的。

4.RILD框架设计

![这里写图片描述](http://img.blog.csdn.net/20161017163850556)


5.rild启动流程分析
1.rild（hardware/ril/rild/rild.c）：仅实现main函数作为整个ril层的入口点，负责完成初始化。

2.libril.so（hardware/ril/libril/*）：与rild结合相当紧密，是其共享库，编译时就已经建立了这一关系.libril.so驻留在rild这一守护进程中，主要完成同上层通信的工作，接受ril请求并传递给libreference-ril.so，同时把libreference-ril.so的反馈传给调用进程。

3.libreference-ril.so（hardware/ril/libreference-ril/*）：rild通过dlopen方式加载，主要负责跟Modem硬件通信。它转换来自librild.so的请求为AT命令，通过串口连接radio,那么参数为这种形式：-d /dev/ttySx（ttyUSBX），同时监控Modem的反馈信息，并传递回libril.so。在初始化时，rild通过符号RIL_Init获取一组函数指针并以此与之建立联系。

RILD进程启动的时序图：

![这里写图片描述](http://img.blog.csdn.net/20161017115236930)

6.rild.c 代码分析

```C

		#include <stdio.h>
		#include <stdlib.h>
		#include <dlfcn.h>
		#include <string.h>
		#include <stdint.h>
		#include <unistd.h>
		#include <fcntl.h>
		#include <errno.h>

		#include <telephony/ril.h>
		#define LOG_TAG "RILD"
		#include <utils/Log.h>
		#include <cutils/properties.h>
		#include <cutils/sockets.h>
		#include <sys/capability.h>
		#include <linux/prctl.h>

		#include <private/android_filesystem_config.h>
		#include "hardware/qemu_pipe.h"

		#define LIB_PATH_PROPERTY   "rild.libpath"
		#define LIB_ARGS_PROPERTY   "rild.libargs"
		#define MAX_LIB_ARGS        16

		static void usage(const char *argv0)
		{
		    fprintf(stderr, "Usage: %s -l <ril impl library> [-- <args for impl library>]\n", argv0);
		    exit(-1);
		}

		extern void RIL_register (const RIL_RadioFunctions *callbacks);

		extern void RIL_onRequestComplete(RIL_Token t, RIL_Errno e,
					   void *response, size_t responselen);

		extern void RIL_onUnsolicitedResponse(int unsolResponse, const void *data,
						size_t datalen);

		extern void RIL_requestTimedCallback (RIL_TimedCallback callback,
					       void *param, const struct timeval *relativeTime);


		static struct RIL_Env s_rilEnv = {
		    RIL_onRequestComplete,
		    RIL_onUnsolicitedResponse,
		    RIL_requestTimedCallback
		};

		extern void RIL_startEventLoop();

		static int make_argv(char * args, char ** argv)
		{
		    // Note: reserve argv[0]
		    int count = 1;
		    char * tok;
		    char * s = args;

		    while ((tok = strtok(s, " \0"))) {
			argv[count] = tok;
			s = NULL;
			count++;
		    }
		    return count;
		}

		/*
		 * switchUser - Switches UID to radio, preserving CAP_NET_ADMIN capabilities.
		 * Our group, cache, was set by init.
		 //切换UID为AID_RADIO  
		 */
		void switchUser() {
		    prctl(PR_SET_KEEPCAPS, 1, 0, 0, 0);
		    setuid(AID_RADIO);

		    struct __user_cap_header_struct header;
		    struct __user_cap_data_struct cap;
		    header.version = _LINUX_CAPABILITY_VERSION;
		    header.pid = 0;
		    cap.effective = cap.permitted = (1 << CAP_NET_ADMIN) | (1 << CAP_NET_RAW);
		    cap.inheritable = 0;
		    capset(&header, &cap);
		}

		/*rild.c仅实现一main函数作为整个ril层的入口点，负责完成初始化*/
		int main(int argc, char **argv)
		{
		    const char * rilLibPath = NULL;
		    char **rilArgv;
		    void *dlHandle;
		    const RIL_RadioFunctions *(*rilInit)(const struct RIL_Env *, int, char **);
		    const RIL_RadioFunctions *funcs;
		    char libPath[PROPERTY_VALUE_MAX];
		    unsigned char hasLibArgs = 0;

		    int i;

		    umask(S_IRGRP | S_IWGRP | S_IXGRP | S_IROTH | S_IWOTH | S_IXOTH);
		    for (i = 1; i < argc ;) {
			if (0 == strcmp(argv[i], "-l") && (argc - i > 1)) {
			    rilLibPath = argv[i + 1];
			    i += 2;
			} else if (0 == strcmp(argv[i], "--")) {
			    i++;
			    hasLibArgs = 1;
			    break;
			} else {
			    usage(argv[0]);
			}
		    }

		    if (rilLibPath == NULL) {
			if ( 0 == property_get(LIB_PATH_PROPERTY, libPath, NULL)) {
			    // No lib sepcified on the command line, and nothing set in props.
			    // Assume "no-ril" case.
			    goto done;
			} else {
			    rilLibPath = libPath;
			}
		    }

		    /* special override when in the emulator */
			/*判断是否为模拟器 */
		#if 1
		    {
			static char*  arg_overrides[3];
			static char   arg_device[32];
			int           done = 0;

		#define  REFERENCE_RIL_PATH  "/system/lib/libreference-ril.so"

			/* first, read /proc/cmdline into memory */
			char          buffer[1024], *p, *q;
			int           len;
			int           fd = open("/proc/cmdline",O_RDONLY);

			if (fd < 0) {
			    RLOGD("could not open /proc/cmdline:%s", strerror(errno));
			    goto OpenLib;
			}
			/*读取/proc/cmdline文件中的内容*/
			do {
			    len = read(fd,buffer,sizeof(buffer)); }
			while (len == -1 && errno == EINTR);

			if (len < 0) {
			    RLOGD("could not read /proc/cmdline:%s", strerror(errno));
			    close(fd);
			    goto OpenLib;
			}
			close(fd);
			/*判断是否为模拟器，对于真机，此处条件为false */
			if (strstr(buffer, "android.qemud=") != NULL)
			{
			    /* the qemud daemon is launched after rild, so
			    * give it some time to create its GSM socket
			    */
			    int  tries = 5;
		#define  QEMUD_SOCKET_NAME    "qemud"

			    while (1) {
				int  fd;

				sleep(1);

				fd = qemu_pipe_open("qemud:gsm");
				if (fd < 0) {
				    fd = socket_local_client(
						QEMUD_SOCKET_NAME,
						ANDROID_SOCKET_NAMESPACE_RESERVED,
						SOCK_STREAM );
				}
				if (fd >= 0) {
				    close(fd);
				    snprintf( arg_device, sizeof(arg_device), "%s/%s",
						ANDROID_SOCKET_DIR, QEMUD_SOCKET_NAME );

				    arg_overrides[1] = "-s";
				    arg_overrides[2] = arg_device;
				    done = 1;
				    break;
				}
				RLOGD("could not connect to %s socket: %s",
				    QEMUD_SOCKET_NAME, strerror(errno));
				if (--tries == 0)
				    break;
			    }
			    if (!done) {
				RLOGE("could not connect to %s socket (giving up): %s",
				    QEMUD_SOCKET_NAME, strerror(errno));
				while(1)
				    sleep(0x00ffffff);
			    }
			}

			/* otherwise, try to see if we passed a device name from the kernel */
			if (!done) do {
		#define  KERNEL_OPTION  "android.ril="
		#define  DEV_PREFIX     "/dev/"
			/*判断/proc/cmdline中的内容是否包含"android.ril="  */
			    p = strstr( buffer, KERNEL_OPTION );
			    if (p == NULL)
				break;

			    p += sizeof(KERNEL_OPTION)-1;
			    q  = strpbrk( p, " \t\n\r" );
			    if (q != NULL)
				*q = 0;

			    snprintf( arg_device, sizeof(arg_device), DEV_PREFIX "%s", p );
			    arg_device[sizeof(arg_device)-1] = 0;
			    arg_overrides[1] = "-d";
			    arg_overrides[2] = arg_device;
			    done = 1;

			} while (0);

			if (done) {
			    argv = arg_overrides;
			    argc = 3;
			    i    = 1;
			    hasLibArgs = 1;
			    rilLibPath = REFERENCE_RIL_PATH; //连接库地址：/system/lib/libreference-ril.so  

			    RLOGD("overriding with %s %s", arg_overrides[1], arg_overrides[2]);
			}
		    }
		OpenLib:
		#endif

		/* 动态库装载 */
		   // switchUser();	/*设置Rild进程的组用户为radio  */
		 /**********************************************************************************
		 打开链接库,librefrence -ril.so(厂商自定义的库  )，从中取出并执行RIL_Init函数，
		 得到RIL_RadioFunctions指针，通过RIL_register()函数注册到libril.so库中
		***********************************************************************************/ 
		    dlHandle = dlopen(rilLibPath, RTLD_NOW);

		    if (dlHandle == NULL) {
			RLOGE("dlopen failed: %s", dlerror());
			exit(-1);
		    }
		 /*************************************************************************************************
		 *RIL_startEventLoop():第一个任务
		 开启EventLoop循环,调用 ril.cpp 中的 RIL_startEventLoop 函数，libril.so 开始循环监听 socket 事件。  
		 **************************************************************************************************/
		    RIL_startEventLoop();
		/*************************************************************************************************
		*第二个任务:RIL_Init
		*硬件访问
		从链接库中（也就是reference-ril.c）寻找RIL_Init函数地址,强制转换为RIL_RadioFunctions的函数指针  
		RIL_Init首先通过参数获取硬件接口的设备文件或模拟硬件接口的socket. 接下来便新开一个线程继续初始化， 即mainLoop
		*****************************************************************************************************/
		    rilInit = (const RIL_RadioFunctions *(*)(const struct RIL_Env *, int, char **))dlsym(dlHandle, "RIL_Init");

		    if (rilInit == NULL) {
			RLOGE("RIL_Init not defined or exported in %s\n", rilLibPath);
			exit(-1);
		    }

		    if (hasLibArgs) {
			rilArgv = argv + i - 1;
			argc = argc -i + 1;
		    } else {
			static char * newArgv[MAX_LIB_ARGS];
			static char args[PROPERTY_VALUE_MAX];
			rilArgv = newArgv;
			property_get(LIB_ARGS_PROPERTY, args, "");/*过属性系统获取参数：rild.libargs*/
			argc = make_argv(args, rilArgv);
		    }

		    // Make sure there's a reasonable argv[0]
		    rilArgv[0] = argv[0];
		/**************************************************************************************
		调用reference-ril.c中的RIL_Init函数进行初始化INIT，同时得到reference-ril的回调函数
		调用RIL_Init函数来初始化rild，传入参数s_rilEnv，返回RIL_RadioFunctions地址  
		**************************************************************************************/
		    funcs = rilInit(&s_rilEnv, argc, rilArgv);
		/*******************************************************************************************
		注册得到的reference的回调函数
		rild通过RIL_register注册这一(onRequest)指针。
		RIL_register中要完成的另外一个任务，就是打开前面提到的跟上层通信的socket接口
		（s_fdListen是主接口，s_fdDebug供调试时使用）。
		*******************************************************************************************/ 
		    RIL_register(funcs);

		done:

		    while(1) {
			// sleep(UINT32_MAX) seems to return immediately on bionic
			sleep(0x00ffffff);
		    }
		}

		```

主入口:rild.c中的main函数，主要完成三个任务：

1. 开启libril.so中的event机制， 在RIL_startEventLoop中，是最核心的由多路I/O驱动的消息循环。

2. 初始化librefrence_ril.so，也就是跟硬件或模拟硬件modem通信的部分（后面统一称硬件）， 通过RIL_Init函数完成。从libreference-ril.so库中取得RIL_Init函数地址，并使用该函数将libril.so库中的RIL_Env接口注册到libreference-ril.so库，同时将libreference-ril.so库中的RIL_RadioFunctions接口注册到到libril.so库中，建立起libril.so库与libreference-ril.so库通信桥梁。

3. 通过RIL_Init获取一组函数指针RIL_RadioFunctions， 并通过RIL_register完成注册，并打开接受上层命令的socket通道。

		 
