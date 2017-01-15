---
layout: post
title: 基于itop4412在Linux最小系统下的USB摄像头视频采集
date: 2017-01-10
categories: Linux,V4L2
tags: [Linux,V4L2]
description: about Linux V4L2 and USB camera study

---

1.实验目的：实现USB摄像头视频采集

2.实验平台：迅为4412开发板，迅为的USB摄像头。

3.实验流程：

	将yuv视频保存到文件中，用pyuv播放器播放。
	
	视频参数：
			尺寸：640 * 480
			YUV格式：YUYV即YUV422

(1)代码如下：

[代码] http://blog.csdn.net/zgyulongfei/article/details/7526249)

非常感谢！


	/*
	*usb camera v4l2 video capture 
	*2014-11-25
	*/
	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <assert.h>
	#include <getopt.h>
	#include <fcntl.h>
	#include <unistd.h>
	#include <errno.h>
	#include <malloc.h>
	#include <sys/stat.h>
	#include <sys/types.h>
	#include <sys/time.h>
	#include <sys/mman.h>
	#include <sys/ioctl.h>
	#include <asm/types.h>
	#include <linux/videodev2.h>
	
	#define CLEAR(x) memset(&(x),0,sizeof(x))
	
	/*三种读取数据方式*/
	typedef enum {
		IO_METHOD_READ,IO_METHOD_MMAP,IO_METHOD_USERPTR,
	}io_method;
	
	struct buffer {
		void *start;
		size_t length;
	};
	
	static char *dev_name = NULL;
	static io_method io = IO_METHOD_MMAP;
	static int fd = -1;
	struct buffer *buffers = NULL;
	static unsigned int n_buffers = 0;
	FILE *fp;
	char *filename = "cam_usb_test.yuv\0";
	
	/*定义打印错误输出*/
	static void errno_exit(const char *s)
	{
		fprintf(stderr,"%s error %d %s\n",s,errno,strerror(errno));
		
		exit(EXIT_FAILURE);
	}
	
	/*重新定义ioctl函数,方便后面使用*/
	static int xioctl(int fd,int request,void *arg)
	{
		int r;
		
		do {
			r = ioctl(fd,request,arg);
		}while(-1 == r && EINTR == errno);
		
		return r;
	}
	
	static void process_image(const void *p,int size)
	{
		fwrite(p,size,1,fp); /*向文件写入一个数据块(以二进制形式)*/
	}
	
	/*获取一帧数据*/
	static int read_frame(void)
	{
		struct v4l2_buffer buf; /*视频缓冲区(队列)信息*/
		unsigned int i;
		
		switch(io) {
			case IO_METHOD_READ:
				if (-1 == read(fd,buffers[0].start,buffers[0].length)) {
					switch(errno) {
						case EAGAIN:
							return 0;
							
						case EIO:
						/*Could ignore EIO,see,spec.*/
						/*fall through*/
						default:
						errno_exit("read");
					}
				}
				
				process_image(buffers[0].start,buffers[0].length);/*保存读到的数据*/
				break;
				
		/*V4L2有一个数据缓存，存放req.count数量的缓存数据。
		数据缓存采用FIFO的方式，当应用程序调用缓存数据时，
		缓存队列将最先采集到的视频数据缓存送出，并重新采集一张视频数据。
		这个过程需要用到两个ioctl命令,VIDIOC_DQBUF和VIDIOC_QBUF*/?
				
			case IO_METHOD_MMAP:
				CLEAR(buf);
				
				buf.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;/*数据流类型,捕获视频图片*/
				buf.memory = V4L2_MEMORY_MMAP;/*内存映射方式*/
				
				if (-1== xioctl(fd,VIDIOC_DQBUF,&buf)) { /*VIDIOC_DQBUF:将已经捕获好视频的内存拉出捕获视频的队列 */  
					switch(errno) {						/*向驱动获取已经存放有视频数据的缓存*/
						case EAGAIN:
							return 0;
						
						case EIO:
						/*Could ignore EIO see spec*/
						/*fall through*/
						default:
							errno_exit("VIDIOC_DQBUF");
					}
				}
				
				assert(buf.index < n_buffers);/*断言:判断括号里条件真假,为假则停止*/
				
				process_image(buffers[buf.index].start,buf.length);
				
				if (-1 == xioctl(fd,VIDIOC_QBUF,&buf)) {  /*VIDIOC_QBUF:将空闲的内存加入可捕获视频的队列*/ 
					errno_exit("VIDIOC_QBUF");  //向驱动传递应用程序已经处理完的缓存
												//即将缓存加入空闲可捕获视频的队列
				}
				break;
				
				case IO_METHOD_USERPTR:
					CLEAR(buf);
					
					buf.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
					buf.memory = V4L2_MEMORY_USERPTR;
					
					if (-1 == xioctl(fd,VIDIOC_DQBUF,&buf)) {
						switch (errno) {
							case EAGAIN:
								return 0;
								
							case EIO:
								/*Could ignore EIO see spec*/
								/*fall through*/
							default:
								errno_exit("VIDIOC_DQBUF");
						}
					}
					
					for(i = 0;i < n_buffers;i++) {
						if (buf.m.userptr == (unsigned long)buffers[i].start &&
							buf.length == buffers[i].length)
							break;
					}
					
					assert(i < n_buffers);
					
					process_image((void *)buf.m.userptr,buf.length);
					
					if (-1 == xioctl(fd,VIDIOC_DQBUF,&buf))
						errno_exit("VIDIOC_DQBUF");
					
					break;
		}
		return 1;
	}
	
	/*完成图像的连续采集*/?
	static void mainloop(void)
	{
		unsigned int count;
		
		count = 100; //读取100帧图像
		
		while(count-- > 0) {
			for (;;){
				fd_set fds;
				struct timeval tv;
				int r;
				
				FD_ZERO(&fds);//初始化fd_set结构体变量
				FD_SET(fd,&fds);//在fd_set结构体上注册文件描述
				
				tv.tv_sec = 2; //2秒
				tv.tv_usec = 0;
				
				r = select(fd+1,&fds,NULL,NULL,&tv);//判断是否可读(即摄像头是否准备好)tv是定时
				
				if (-1 == r) {
					if (EINTR == errno)
						continue;
					
					errno_exit("select");
				}
				
				if (0 == r) { /*时间到，返回0*/
					fprintf(stderr,"select timeout!\n");
					exit(EXIT_FAILURE);
				}
				
				if (read_frame())/*如果可读，执行read_frame函数,并跳出循环*/
					break;
			}
		}
	}
	
	/*停止视频的采集 */
	static void stop_capturing(void)
	{
		enum v4l2_buf_type type;
		
		switch(io) {
			case IO_METHOD_READ:
				/*Nothing to do*/
				break;
			case IO_METHOD_MMAP:
			case IO_METHOD_USERPTR:
			type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
			
			if (-1 == xioctl(fd,VIDIOC_STREAMOFF,&type))  /*关闭视频流*/  
				errno_exit("VIDIOC_STREAMOFF");
			
			break;
		}
	}
	
	/*开始视频采集*/
	static void start_capturing(void)
	{
		unsigned int i;
		
		enum v4l2_buf_type type;
		
		switch(io) {
			case IO_METHOD_READ:
				/*Nothing is to do*/
				break;
				
			case IO_METHOD_MMAP:
				for(i = 0;i < n_buffers;++i) {
					struct v4l2_buffer buf;
			/*for循环的作用是：将申请到的帧缓冲全部入队列
			以便存放采集到的数据.VIDIOC_QBUF,struct?v4l2_buffer*/			
					CLEAR(buf);
					
					buf.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
					buf.memory = V4L2_MEMORY_MMAP;
					buf.index = i;
					
					if (-1 == xioctl(fd,VIDIOC_QBUF,&buf))//申请到的缓冲进入列队
						errno_exit("VIDIOC_QBUF");
				}
				
				type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
				
				if (-1 == xioctl(fd,VIDIOC_STREAMON,&type))//开始捕捉图像数据
					errno_exit("VIDIOC_STREAMON");
				
				break;
				
			case IO_METHOD_USERPTR:
				for(i = 0;i < n_buffers;++i) {
					struct v4l2_buffer buf;
					
					CLEAR(buf);
					
					buf.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
					buf.memory = V4L2_MEMORY_USERPTR;
					buf.index = i;
					buf.m.userptr = (unsigned long)buffers[i].start;
					buf.length = buffers[i].length;
					
					if (-1 == xioctl(fd,VIDIOC_QBUF,&buf))
						errno_exit("VIDIOC_QBUF");
				}
				
				type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
				
				if (-1 == xioctl(fd,VIDIOC_STREAMON,&type))
					errno_exit("VIDIOC_STREAMON");
				
				break;
			}
	}
	
	/*注销设备文件*/
	static void uninit_device(void)
	{
		unsigned int i;
		
		switch(io) {
			case IO_METHOD_READ:
				free(buffers[0].start);
				break;
				
			case IO_METHOD_MMAP:
				for(i = 0; i < n_buffers;++i) 
					if (-1 == munmap(buffers[i].start,buffers[i].length))
						errno_exit("munmap");
					
					break;
					
			case IO_METHOD_USERPTR:
				for(i = 0;i < n_buffers;++i) 
					free(buffers[i].start);
					
				break;
		}
		free(buffers);
	}
	
	static void init_read(unsigned int buffer_size)
	{
		buffers = calloc(1,sizeof(*buffers));/*在内存动态存储区中分配n块长度为“size”字节的连续区域*/
		
		if (!buffers) {
			fprintf(stderr,"Out of memory!\n");
			exit(EXIT_FAILURE);
		}
		
		buffers[0].length = buffer_size;
		buffers[0].start = malloc(buffer_size);
		
		if (!buffers[0].start) {
			fprintf(stderr,"Out of memory!\n");
			exit(EXIT_FAILURE);
		}
	}
	
	static void init_mmap(void)
	{
		struct v4l2_requestbuffers req; //向驱动申请帧缓冲，一般不超过5个
		
		CLEAR(req);
		
		req.count = 4; //缓存数量，也就是在缓存队列中保持多少张照片
		req.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
		req.memory = V4L2_MEMORY_MMAP;
		
		if (-1 == xioctl(fd,VIDIOC_REQBUFS,&req)) { //申请count大小的缓存
			if (EINVAL == errno) {
				fprintf(stderr,"%s does not support memory mapping\n",dev_name);
				exit(EXIT_FAILURE);
			}
			else {
				errno_exit("VIDIOC_REQBUFS");
			}
		}
		
		if (req.count < 2) {
			fprintf(stderr,"insufficient buffer memory %s \n",dev_name);
			exit(EXIT_FAILURE);
		}
		
		buffers = calloc(req.count,sizeof(*buffers));
		
		if (!buffers) {
			fprintf(stderr,"Out of memory!\n");
			exit(EXIT_FAILURE);
		}
	/*将申请到的帧缓冲映射到用户空间,这样就可以直接操作采集到的帧了,而不必去复制*/
		for(n_buffers = 0;n_buffers < req.count;++n_buffers) {
			struct v4l2_buffer buf;
			
			CLEAR(buf);
			
			buf.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
			buf.memory = V4L2_MEMORY_MMAP;
			buf.index = n_buffers;//读取缓存 
			
			if (-1 == xioctl(fd,VIDIOC_QUERYBUF,&buf))/*映射用户空间,向驱动查询申请到的内存*/   
				errno_exit("VIDIOC_QUERYBUF");
			
			buffers[n_buffers].length = buf.length;
			/*转换成相对地址:内核-->用户空间.offset:没帧相对及地址的偏移*/
			buffers[n_buffers].start = mmap(NULL,buf.length,PROT_READ | PROT_WRITE,MAP_SHARED,fd,buf.m.offset);
			
			if (MAP_FAILED == buffers[n_buffers].start)
				errno_exit("mmap");
		}
	}
	
	static void init_userp(unsigned int buffer_size)
	{
		struct v4l2_requestbuffers req;
		unsigned int page_size;
		
		page_size = getpagesize();
		buffer_size = (buffer_size + page_size-1)&~(page_size-1);
		
		CLEAR(req);
		
		req.count = 4;
		req.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
		req.memory = V4L2_MEMORY_USERPTR;
		
		if (-1 == xioctl(fd,VIDIOC_REQBUFS,&req)) {
			if (EINVAL == errno) {
				fprintf(stderr,"%s does not support user pointer i/o\n",dev_name);
				exit(EXIT_FAILURE);
			}
			else {
				errno_exit("VIDIOC_REQBUFS");
			}
		}
		
		buffers = calloc(4,sizeof(*buffers));
		
		if (!buffers) {
			fprintf(stderr,"Out of memory!\n");
			exit(EXIT_FAILURE);
		}
		
		for(n_buffers = 0;n_buffers < 4;++n_buffers) {
			buffers[n_buffers].length = buffer_size;
			buffers[n_buffers].start = memalign(page_size,buffer_size);
			
			if (!buffers[n_buffers].start) {
				fprintf(stderr,"Out of memory!\n");
				exit(EXIT_FAILURE);
			}
		}
	}
	
	/*初始化*/
	static void init_device(void)
	{
		struct v4l2_capability cap; //设备能力
		struct v4l2_cropcap cropcap; //与驱动修剪能力相关的结构体
		struct v4l2_crop crop; //设置采集窗口?
		struct v4l2_format fmt; //设置视频点阵格式和大小
		unsigned int min;
		
		/*取得设备的capability，看看设备具有什么功能,
		比如是否具有视频输入,或者音频输入输出等。VIDIOC_QUERYCAP?,struct?v4l2_capability*/
		
		if (-1 == xioctl(fd,VIDIOC_QUERYCAP,&cap)) { //获取摄像头参数
			if (EINVAL == errno) { //此错误表示设备文件的驱动程序不能处理request或者arg
				fprintf(stderr,"%s is no v4l2 device\n",dev_name);
				exit(EXIT_FAILURE);
			}
			else {
				 errno_exit("VIDIOC_QUERYCAP");
			}
		}
		
		/*不具备设备支持的捕捉模式，cap.capabilities的常见值有
		V4L2_CAP_VIDEO_CAPTURE|V4L2_CAP_STREAMING,后者表示一个视频捕捉设备具有数据流控制模式?*/
		if (!(cap.capabilities & V4L2_CAP_VIDEO_CAPTURE)) {
			fprintf(stderr,"%s is no video capture device\n",dev_name);
			exit(EXIT_FAILURE);
		}
		
		switch(io) {
			case IO_METHOD_READ: //直接读写方式
				if (!(cap.capabilities & V4L2_CAP_READWRITE)) {
					fprintf(stderr,"%s does not support read i/o \n",dev_name);
					exit(EXIT_FAILURE);
				}
				
			break;
			
			case IO_METHOD_MMAP: //映射方式
			case IO_METHOD_USERPTR: //用户指针模式 
				if(!(cap.capabilities & V4L2_CAP_STREAMING)) {
					fprintf(stderr,"%s does not support streaming !/i/o \n",dev_name);
					exit(EXIT_FAILURE);
				}
				
				break;
		}
		
		CLEAR(cropcap);
		
		/*先设置v4l2_cropcap的type域,再通过VIDIO_CROPCAP操作命令获取设备捕捉能力的参数,
		保存于v4l2_cropcap结构体中,包括bounds(最大捕捉方框的左上角坐标和宽高)
		defrect(默认捕捉方框的左上角坐标和宽高)等。*/?
		cropcap.type = V4L2_BUF_TYPE_VIDEO_CAPTURE; //视频捕捉模式
		
		if (0 == xioctl(fd,VIDIOC_CROPCAP,&cropcap)) {
			crop.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
			crop.c = cropcap.defrect;
		
	
			if (-1 == xioctl(fd,VIDIOC_S_CROP,&crop)) {//配置视频信号的边框传送一个capture
				switch(errno) {						//或overlay模式下的取景范围	
					case EINVAL:
					/*Cropping not supported*/
						break;
					default:
					/*error ignore*/
						break;
				}
			}
		}
		else {
			/*Errors ignore*/
		}
		
		CLEAR(fmt);
	
	/*设置视频的制式和帧格式,制式包括PAL,NTSC,帧的格式个包括宽度和高度等*/	
		fmt.type = V4L2_BUF_TYPE_VIDEO_CAPTURE;
		fmt.fmt.pix.width = 640;
		fmt.fmt.pix.height = 480;
		fmt.fmt.pix.pixelformat = V4L2_PIX_FMT_YUYV;//视频数据的存储类型 
		fmt.fmt.pix.field = V4L2_FIELD_INTERLACED;//设置当前的频捕捉模式(隔行扫描)
		
		if (-1 == xioctl(fd,VIDIOC_S_FMT,&fmt))//设置视频格式
			errno_exit("VIDIOC_S_FMT");
		
		min = fmt.fmt.pix.width * 2;
		if (fmt.fmt.pix.bytesperline < min)
			fmt.fmt.pix.bytesperline = min;
		min = fmt.fmt.pix.bytesperline * fmt.fmt.pix.height;
		if (fmt.fmt.pix.sizeimage < min) 
			fmt.fmt.pix.sizeimage = min;
		
		switch(io) {
			case IO_METHOD_READ:
				init_read(fmt.fmt.pix.sizeimage);
				break;
				
			case IO_METHOD_MMAP:
				init_mmap();
				break;
			case IO_METHOD_USERPTR:
				init_userp(fmt.fmt.pix.sizeimage);
				break;
		}
	}
	
	//注销设备文件
	static void close_device(void)
	{
		if (-1 == close(fd))
			errno_exit("close");
		
		fd = -1;
	}
	
	//打开设备文件
	static void open_device(void)
	{
		struct stat st;
		
		if (-1 == stat(dev_name,&st)) {//stat函数通过文件名dev_name获取文件信息
			fprintf(stderr,"Cannot identify '%s' :%d,%s\n",dev_name,errno,strerror(errno));
			exit(EXIT_FAILURE);
		}
		
		if (!S_ISCHR(st.st_mode)) { //stat函数通过文件名dev_name获取文件信息
			fprintf(stderr,"%s is no device\n",dev_name);
			exit(EXIT_FAILURE);
		}
		
		fd = open(dev_name,O_RDWR | O_NONBLOCK,0);
	/*关于阻塞模式和非阻塞模式:应用程序能够使用阻塞模式或非阻塞模式打开视频设备;
	如果使用非阻塞模式调用视频设备,即使尚未捕获到信息,
	驱动依旧会把缓存(DQBUFF)里的东西返回给应用程序 */
	
		if (-1 == fd) {
			fprintf(stderr,"Cannot open '%s' %d,%s\n",dev_name,errno,strerror(errno));
			exit(EXIT_FAILURE);
		}
	}
	
	static void usage(FILE *fp,int argc,char **argv)
	{
		fprintf(fp,"Usage:%s [options]\n\n"
					"Options:\n"
					"-d | --device name Video device name[/dev/video]\n"
					"-h | --help 	Print this message\n"
					"-m | --mmap    Use memory mapped buffers\n"
					"-r | --read    Use read()calls\n"
					"-u | --userp   Use application allocated buffers\n"
					"",argv[0]);
	}
	
	static const char short_options[] = "d:hmru";//选项表设置(加了冒号的选项必须加参数)
	
	static const struct option long_options[] = { { "device", required_argument,
			NULL, 'd' }, { "help", no_argument, NULL, 'h' }, { "mmap", no_argument,
			NULL, 'm' }, { "read", no_argument, NULL, 'r' }, { "userp", no_argument,
			NULL, 'u' }, { 0, 0, 0, 0 } };
			
	int main(int argc,char **argv) 
	{
		dev_name = "/dev/video4";
		
		for(;;) {
			int index;
			int c;
		
			/*用于长选项的命令行解释*/	
			c = getopt_long(argc,argv,short_options,long_options,&index);
			
			if (-1 == c)
				break;
			
			switch(c) {
				case 0:
					break;
					
				case 'd':
				dev_name = optarg; //当处理带参数的选项时,全局变量optarg会指向它的参数(/dev/video4)
					break;
					
				case 'h':
					usage(stdout,argc,argv);
					exit(EXIT_SUCCESS);
					
				case 'm':
					io = IO_METHOD_MMAP;
						break;
						
				case 'r':
					io = IO_METHOD_READ;
						break;
						
				case 'u':
					io = IO_METHOD_USERPTR;
						break;
						
				default:
					usage(stderr,argc,argv);
					exit(EXIT_FAILURE);
			}
		}
		
		open_device();
		
		init_device();
		
		start_capturing();
		
		fp = fopen(filename,"wa+");
		
		mainloop();
		
		fclose(fp);
		
		stop_capturing();
		
		uninit_device();
		
		close_device();
		
		exit(EXIT_SUCCESS);
		
		return 0;
	}



(2)交叉编译

![这里写图片描述](http://img.blog.csdn.net/20170110223547323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

(3)拷贝到4412开发板运行

![这里写图片描述](http://img.blog.csdn.net/20170110223911038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
____
运行时候可以得到yuv格式的视频：

![这里写图片描述](http://img.blog.csdn.net/20170110224040809?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

(4)将采集到的视频通过U盘拷贝到电脑，通过pYUV进行播放，可以看到采集的视频

pYUV播放参数设置：

![这里写图片描述](http://img.blog.csdn.net/20170110224252106?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

播放效果：

![这里写图片描述](http://img.blog.csdn.net/20170110224418233?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0MTg4MTE3MDc5NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

参考：

1.[ V4L2采集yuv视频花屏：Linux视频采集与编码（一）
](http://blog.csdn.net/zgyulongfei/article/details/7526249)

2.[一篇不错的v4l2入门文档 
](http://blog.chinaunix.net/uid-26851094-id-3270803.html)

3.[Camera 驱动和编程](http://www.linuxidc.com/Linux/2012-12/77160.htm)

4.[基于V4l2的USB摄像头视频采集原理
](http://www.wnavy.com/archives/515)

5.[Video4Linux下USB摄像头驱动和视频采集的实现](http://www.go-gddq.com/html/ShuZiShiPinJiShu/2012-04/965299p2.htm)



