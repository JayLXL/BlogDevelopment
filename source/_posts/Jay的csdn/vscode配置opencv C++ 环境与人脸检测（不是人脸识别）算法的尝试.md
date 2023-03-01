---
title: vscode配置opencv C++ 环境与人脸检测（不是人脸识别）算法的尝试
categories: 笔记
tags: 
- 机器学习
- csdn
---
​[csdn](https://blog.csdn.net/m0_61322309/article/details/124214186?spm=1001.2014.3001.5501)
估计这学期都去不了学校了，家里蹲大学马上要线上期中考。。。在家呆久了无聊想学点机器视觉的东西，之前在学校双创课上做过python的机器视觉实验，想起来挺有意思的，但是还是比较喜欢C++，就找到了这个看上去还不错的C++开源项目 ，opencv。

首先是配置opencv环境。看了很多教程，下面这个是最详细的，最后是参照上面这篇博客 成功完成了配置，感谢大佬。之前出现过很多奇怪的问题，也学到了一些经验，在这里记录一下。


1.MinGW的版本问题：之前是在电脑里装了MinGW环境的，所以直接按照教程安装了Cmake，结果configure的时候一直报错“MinGW缺少mingw-make.exe”，查找MinGW目录，明明有这个文件。后来发现MinGW不是最新版的，而且有博客提起过，只有seh版的MinGW才能Cmake中成功的configure。于是删了MinGW，重新安装了一下MinGW。而且发现由于奇怪的网络问题，MinGW不能在线安装了，于是下载安装压缩包安装上了版本正确的MinGW。好像这种python库或c++库的安装都很有时效性，各软件版本正确是很关键的。

2.Cmake的configure过程，要下载一些东西，有时候由于奇怪的问题下载不了，列表会变红，这时候可以通过科学上网，或者手动搜索下载无法下载的文件补上。新建一个文件夹作为“where to build the binaries"地址选项，不然后面会很乱。

3.由于之前配置过了vscode的各种json文件，就直接在上面改了，开始我只在IncludePath下添加了新的opencv的相关头文件路径，结果是编辑器检测没报错，按住ctrl键也可以打开查看源文件，但是一编译就报错无法打开头文件，查了好多资料才知道，c_cpp_properties.josn中添加的路径只是告诉编辑器的，让编辑器不打红色波浪线，但是此时编译器还不知道这个引用，要在tasks.json中添加相关的路径才会正常编译。并且了解到了.dll(用于动态链接库导出函数，在运行期间链接）.lib(用于动态链接库导出函数，在编译期间）和.a（静态库文件）的区别。以及“-I"（小写L） "-I”（大写i）命令，相当于添加动态程序库链接，静态文件库链接之类的。
```cpp
    "tasks": [
        {
            "type": "shell",
            "label": "opencv",
            "command": "D:/mingw64/bin/g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${workspaceFolder}\\Debugger\\${fileBasenameNoExtension}.exe",
                "D:/opencv/build/x64/mingw/bin/libopencv_world455.dll",
                "-I",
                "D:/opencv/build/x64/mingw/install/include",//需要添加的路径
                "-I",
                "D:/opencv/build/x64/mingw/install/include/opencv2",//需要添加的路径
            ],
            "options": {
                "cwd": "D:/mingw64/bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
```
4.发现搞清楚这个问题之后，face.cpp头文件还是会报错，结果是最新版的opencv把这个头文件暂时下架了，原因是不稳定，要重新开发，网上教程说要在另一个库里opencv_contrib自行下载。。。这个源文件。

搞清楚了环境的安装配置之后，头文件终于不报错了，也可以正常编译了。马上按照网上的代码学习尝试了下opencv自带的人脸检测算法。在刚刚下载的opencv中，可以找到这些文件。

 据说这些文件是github大佬们将模型训练之后得到的数据样本库，通过调用一个opencv里的函数就可以利用这些模型的数据做相关的一些实验了。里面有各种各样的模型，有的是检测人脸的，有的是检测上半身的，有的是检测眼睛部位的。总之感觉都很神奇。

然后是学习代码时间，了解一下相关的工序，将图片灰度化，均衡化什么的，建立连级检测器，导入刚刚的分类器训练文件，熟悉下opencv的基本文件操作：

1-遍历文件夹内的文件，通过glode函数实现，大概就是读取指定文件夹下的路径保存在一个vector中；

2-imread()读取图片文件，namewindow()显示图片的窗体。imwrite()写入图片；

之后我利用下面的代码尝试了下，在一个文件夹下挑选出含有人脸的jpg或png格式的图片，看看效果。
​
```cpp
#include "opencv2/objdetect.hpp"
#include "opencv2/highgui.hpp"
#include "opencv2/imgproc.hpp"
#include <iostream>
#include <string>
#include<cstdlib>
#include<filesystem>
#include<fstream>
#include<algorithm>
using namespace std;
using namespace cv;

CascadeClassifier face_cascade[4];//建立连级采样器

bool detect( Mat frame ,int i,cv::String name)
{
    Mat frame_gray;
	Mat converse;
    cvtColor( frame, frame_gray, COLOR_BGR2GRAY );//颜色空间转换函数，将图像转化为灰度图像
    equalizeHist( frame_gray, frame_gray );//直方图均衡化函数
    std::vector<Rect> faces;//RECT类用于描述矩形
	//flip(frame_gray,converse,1);//反转图片,可用于侧脸检测
	bool isfind=false;
	face_cascade[1].detectMultiScale(frame_gray, faces);//用于检测的函数
	if(faces.size()){
		cout<<"第"<<i+1<<"张图片中发现"<<faces.size()<<"张脸"<<endl;
		string a;
		char s[100];
		itoa(i+1,s,10);
		a=s;
		imwrite("C:/Users/notbadhhhh/Desktop/4\\"+a+".jpg",frame);
		a=name;
		int resort=remove(a.c_str());
		//namedWindow("w");
		//imshow( "w", frame );
		//waitKey(3000);
		//显示图片
		return true;
	}
	else {
		cout<<"第"<<i+1<<"张图片中未发现人脸"<<endl;
		return false;
	}
}

bool pre_check(){
	if(!face_cascade[1].load("C:/Users/notbadhhhh/Desktop/haarcascade_frontalface_default.xml")) //opencv自带的人脸识别,需要下载
    {
        cout << "--(!)Error loading face cascade\n";
		system("pause");
        return 0;
    }
	if(!face_cascade[2].load("C:/Users/notbadhhhh/Desktop/haarcascade_profileface.xml")) //opencv自带的人脸识别,需要下载
    {
        cout << "--(!)Error loading face cascade\n";
		system("pause");
        return 0;
    }
	if(!face_cascade[3].load("C:/Users/notbadhhhh/Desktop/haarcascade_eye_tree_eyeglasses.xml")) //opencv自带的人脸识别,需要下载
    {
        cout << "--(!)Error loading face cascade\n";
		system("pause");
        return 0;
    }
	if(!face_cascade[4].load("C:/Users/notbadhhhh/Desktop/haarcascade_eye.xml")) //opencv自带的人脸识别,需要下载
    {
        cout << "--(!)Error loading face cascade\n";
		system("pause");
        return 0;
    }
	else return 1;
}//可同时导入多个训练模型文件

int main()
{
	int sum=0;
	system("chcp 65001"),system("cls");//让编译器能输出中文
	bool is=pre_check();
    if(!is)return 1;
	string origin_deposite="C:/Users/notbadhhhh/Desktop/python1.4";//目标文件夹
	string pattern_jpg=origin_deposite+"/*jpg";
	string pattern_png=origin_deposite+"/*png";
	vector<cv::String> image_files_jpg,image_files_png;
	glob(pattern_jpg,image_files_jpg);
	glob(pattern_png,image_files_png);
	for(int i=0;i<image_files_jpg.size();i++){
		Mat pic=imread(image_files_jpg[i]);
		if (pic.empty())cout << " wrong" << endl;
		else{
			bool b=detect(pic,i,image_files_jpg[i]);
			if(b)sum++;
		}
	}//遍历文件夹下的jpg图片
	for(int i=0;i<image_files_png.size();i++){
		Mat pic=imread(image_files_png[i]);
		if (pic.empty())cout << " wrong" << endl;
		else{
			bool b=detect(pic,i,image_files_png[i]);
			if(b)sum++;
		}
	}//遍历文件夹下的png图片
	cout<<"一共筛选出了"<<sum<<"张含有人脸的照片"<<endl;
	system("pause");
    return 0;
}
```
反复测试了多个训练器模型文件后，发现这些模型的识别准确率远低于我的预期，误检率和漏检率都非常的高。。。反复调试了detectMultiScale的七个参数，发现正确率始终不是很高。用三十张含有人脸的照片，最好的一次检出了二十一张含有人脸，也就是70%左右。不知道是我的参数没设置合适还是其他什么原因，正确率基本上不超过70%。检测人眼的，和检测上半身的正确率更离谱。。。上网也没查到什么资料（一查人脸检测的讨论，出来的都是人脸识别）。而且检测很耗时，基本上一张图片要几秒钟。

虽然这不是最好的人脸检测算法，但是也不至于这么低吧，我以为至少有百分之九十以上准确率。结果比较失望。了解到还有一些其他的比较优秀的人脸检测算法，但是限于缺少获取途径和能力（好像他们不是开源的），无法再简单的实践体验了。

想问问有大佬知道这个算法的最优正确率有多高吗？或者怎么改进提高检测正确率？