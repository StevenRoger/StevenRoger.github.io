---
title: C#调用C++动态库并回调C#函数
description: 界面层使用C#，需要调用C++动态库与后台进行逻辑交互，并实时回调C#函数，返回数据
categories: [C++, C#]
tags: [C++, C#]
---

需求是这样的，C++动态库负责与保持服务器连接，交互数据，C#作为界面层。动态库发送请求，也需要实时接收服务器端的指令，并传递到C#，轮询动态库，取数据，是一种办法；但还有一种方法就是用回调函数，让动态库接收数据后，将指令传递到界面层，并自动执行操作。 
新手，一步步摸索出来这种方法，记下来。

C++动态库，声明一个函数指针:
```c++
typedef void (*RemoteMsgCallBack)(int nTasKLevel, int nPageID, int nAction, int nTaskID,const char* strParamDatas);
```

接着声明一下该指针类型的变量:
```c++
RemoteMsgCallBack g_RemoteMsgCall;
```

这个我个人用于全局保存从C#端传过来的函数地址。其他很多类都要用到这个回调函数，所以其他需要用到回调函数的ccp文件里，就需要再声明一次：
```c++
extern RemoteMsgCallBack g_RemoteMsgCall;
```

然后就是导出一个接口，用来注册C#的函数地址了 
头文件里：
```c++
#ifndef DLL_API
#define DLL_API extern "C" _declspec(dllexport)
#endif

DLL_API void SetCallback(CPPCallback callback);
```

cpp文件里：
```C++
DLL_API void SetCallback(CPPCallback callback)
{
    g_RemoteMsgCall = callback;
}
```

这样，cpp文件的任何位置，需要回调用C#函数，只要调g_RemoteMsgCall就可以了。
```
g_RemoteMsgCall(nTastLevel, nPageID, nAction, nTaskID, strParam.c_str());
```

接着就是C#端的代码了，因为C#是没有函数指针的概念的，但是都知道函数的委托，就跟函数指针很像，这里确实也要用委托。不过就是在这里需要注意点。
```c#
//没有这句声明，就会报访问内存出错
[UnmanagedFunctionPointer(CallingConvention.Cdecl)]
public delegate void SetRemoteMsgCallBack(int nTasKLevel, int nPageID, int nAction, int nTaskID, StringBuilder strParamDatas);

public static SetRemoteMsgCallBack setRemoteMsgCallBack;

[DllImport("PatientClient.dll", EntryPoint = "SetCallback", CallingConvention = CallingConvention.Cdecl, CharSet = CharSet.Ansi)]
public static extern void SetCallback(CSCallback callback);

//在一个函数里设置注册回调
public void RegistRemoteMsgCallBack()
{
    setRemoteMsgCallBack += SetRemoteMsg;
    SetCallback(setRemoteMsgCallBack);
}

//与委托 签名相同的函数
public void SetRemoteMsg(int nTasKLevel, int nPageID, int nAction, int nTaskID, StringBuilder strParamDatas)
{
    System.Console.WriteLine("nTasKLevel: " + nTasKLevel + " nPageID " + nPageID + " nAction " + nAction 
    + " nTaskID " + nTaskID + "\nparams  " + strParamDatas);
}
```

到此为止，C++动态库就能回调C#端的函数了。 
这里我还犯的一个错，就是总是在调试状态跟消息，没有写完整的运行程序，导致一到断点，虽然动态库收到消息，C#端还是没能实时刷新数据，而且动态库的日志也像卡死在接收数据线程里了。