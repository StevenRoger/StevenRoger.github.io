---
title: OpenSSL使用记录
description: OpenSSL使用记录，遇到的一些问题，以及解决方法
categories:
- C++
tags:
- C++
---

对于OpenSSL一直以来，我只在Linux上用到它的一些加密算法，编译源码，然后链接使用也很方便，没有出过什么问题。然而它主要是用来进行https安全通信的，只有项目需要，才主动去学习通信相关的API。本文使用的openssl版本为1.1.1-dev，正常情况下，我也不会用dev版:
> If you have any problems with OpenSSL then please take the following steps 
first: 
- Download the latest version from the repository 
to see if the problem has already been addressed 
- Configure with no-asm 
- Remove compiler optimisation flags

所在公司的项目，基本上都用vs2008，于是就下来源码，心安理得地按照README上的步骤，开始安装perl，用vs2008的命令行编译。结果就是各种改环境变量，安装缺少的程序，然并卵！ 
在放弃之前，就用vs2010试一下，重新解压下载过来的代码，用vs2010的命令行编译

```
perl Configure VC-WIN64A no-asm
```

一次性通过。然后就是nmake test、nmake install，一路顺利通过了。 
接着就是写代码了，源码中有demo，作为客户端，可以参考sconnect.c。 
个人需求不同，就写了一个收发的函数，记录BIO的用法，还有遇到的问题。其中也参考了IBM的[Secure programming with the OpenSSL API](https://www.ibm.com/developerworks/library/l-openssl/index.html)

```c++
void HttpsPostRequest(const string& strSvrIP, const int nSvrPort, const string& request, string& strRet)
{
    BIO * bio;
    SSL *ssl;
    SSL_CTX *sslCtx = NULL;

    /* Initializing OpenSSL */
    SSL_load_error_strings();
    ERR_load_BIO_strings();
    OpenSSL_add_all_algorithms();

    //Setting up the SSL pointers
    sslCtx = SSL_CTX_new(SSLv23_client_method());
    SSL_CTX_set_verify(sslCtx, SSL_VERIFY_NONE, NULL);

    bio = BIO_new_ssl_connect(sslCtx);
    BIO_get_ssl(bio, &ssl);
    SSL_set_mode(ssl, SSL_MODE_AUTO_RETRY);

    /* Attempt to connect */
    stringstream ss;
    ss<<strSvrIP<<":"<<nSvrPort;
    string strHostName = ss.str();
    BIO_set_conn_hostname(bio, strHostName.c_str());
    /* Verify the connection opened and perform the handshake */
    if(BIO_do_connect(bio) <= 0)
    {
        fprintf(stderr, "Error attempting to connect\n");
    }

    BIO_write(bio, request.c_str(), request.size());

    int nTransferUnit = 24576;
    char pszUnitBuf[262144] = {0};

    int nRecvTemp = 0;
    int nOffset = 0;
    for(;;)
    {
        nRecvTemp = BIO_read(bio, pszUnitBuf + nOffset, nTransferUnit);
        if(nRecvTemp <= 0) break;
        nOffset += nRecvTemp;
    }
    strRet = pszUnitBuf;

    /* To free it from memory*/
    BIO_free_all(bio);

    SSL_CTX_free(sslCtx);
}
```

这里我没有读取pem文件验证证书有效性，暂时也没发现什么问题。就是在用BIO进行读数据的时候，在发送报文的头里，需要加上
```
Connection: Close
```

这对参数，不然连接默认是keep-alive，socket就会一直阻塞在BIO_read这里，即使一次数据已经读取完。对https协议了解的不多，完全只是为了调用第三方的接口而做，socket阻塞的问题暂时没有深究，知道的大神请赐教。 
在网上下载到的编译好的openssl库，很多都是32位的库，而且版本比较旧，动态库仍然还是libeay32.dll和ssleay32.dll这种名字，这里我就附上我编译好的相关依赖文件。

[OpenSSL-1.1.1-win64](http://download.csdn.net/detail/u011414997/9707681)