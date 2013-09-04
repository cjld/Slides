# Qt下socket介绍 与 qmake 分析 <br> <small>计25 梁盾</small>

-------------

# 大纲
- ## Qt下socket介绍
- ## Qt多线程介绍
- ## QMake以及信号槽分析

---------------

# Qt下的socket运用

Qt对几种不同协议的socket进行了封装，均继承自`QAbstractSocket`

-   ##UDP协议

    `QUdpSocket` : 此类提供对UDP的socket

-   ##TCP协议

    - `QTcpSocket` : 此类提供对TCP的socket  
    - `QTcpServer` : 此类提供TCP服务

---------------

# QUdpSocket <small> 功能简介 </small>

---------------

# QUdpSocket <small> 功能简介 </small>

## 几个重要的函数
-   ##发送数据

        !c++
        qint64 QUdpSocket::writeDatagram(
            const char *data, qint64 size,              //数据
            const QHostAddress &address, quint16 port)  //地址端口

-   ##接收数据

        !c++
        qint64 QUdpSocket::readDatagram(
            char * data, qint64 maxSize,                //数据
            QHostAddress *address=0, quint16 *port=0)   //地址端口

-------------

# QUdpSocket <small> 功能简介 </small>

## 几个重要的函数
-   ##端口绑定

        !c++
        bool QAbstractSocket::bind(
            const QHostAddress &address,                //地址
            quint16 port = 0,                           //端口
            BindMode mode = DefaultForPlatform)         //绑定模式

-   ##接收数据信号

        !c++
        void QIODevice::readyRead() //当绑定的端口接受到数据时运行
     

--------------

# QTcpSocket <small> 功能简介 </small>

--------------

# QTcpSocket <small> 功能简介 </small>

因为不能绑定端口，功能比 `QUdpSocket` 更简单

## 几个重要的函数
-   ##读取/发送数据

        !c++
        qint64 QIODevice::read(char * data, qint64 maxSize)
        qint64 QIODevice::write(const char * data, qint64 maxSize)

-   ##连接服务器
 
        !c++
        void QAbstractSocket::connectToHost(
            const QString & hostName,                   //服务器地址
            quint16 port,                               //服务器端口
            OpenMode openMode = ReadWrite, NetworkLayerProtocol protocol = AnyIPProtocol)

------------

# QTcpServer <small> 功能简介 </small>

------------

# QTcpServer <small> 功能简介 </small>

此类用于架设TCP服务器，优点是非阻塞

-   ##监听端口

        !c++
        bool QTcpServer::listen(
            const QHostAddress & address = QHostAddress::Any, 
            quint16 port = 0)

-   ##客户端连接信号

        !c++
        void QTcpServer::newConnection() // [signal]

-   ##获得连接服务器的socket

        !c++
        QTcpSocket* QTcpServer::nextPendingConnection()

###TIPS : 活用 Qt creator 的 F1(帮助)

-----------

#QThread <small> 功能简介 </small>

-----------

#QThread <small> 功能简介 </small>

##从 QThread 继承并重载虚函数 run()

    !c++
    class Thread : public QThread { 
      Q_OBJECT 
      public: 
        Thread(QObject* parent=0):QThread(parent){} 
        void run() {
          //你的代码
        } 
    }; 

-   ##开始运行

        !c++
        void QThread::start()

-   ##线程运行结束信号

        !c++
        void QThread::finished() // [signal]

-----------

#QMake以及信号槽分析 <small> qmake到底干了什么 </small>

-----------

#QMake以及信号槽分析 <small> qmake到底干了什么 </small>

##问题

- ###QMake干了什么
- ###信号槽是如何实现的
- ###Qt代码可移植性

-----------

#QMake干了什么

-   ##分析ui文件，生成代码
-   ##分析从`QObject`继承而来的类，生成moc代码
-   ##生成makefile文件

-------------

#信号槽是如何实现的

-   ##slots signals emit 保留字?

    实际上并不是什么保留字，而是一个宏定义，用于给qmake做语法分析
    
        !c++
        //from qobjectdefs.h
        #define slots
        #define signals public
        #define emit

-   ##signal函数为何不用定义?

    在moc文件中定义了

-   ##SLOT(a) SIGNAL(a) 是什么?

        !c++
        //from qobjectdefs.h
        #define SLOT(a)     qFlagLocation("1"#a QLOCATION)
        #define SIGNAL(a)   qFlagLocation("2"#a QLOCATION)

    用于进行信号槽的匹配

---------

#Qt代码可移植性

-   ##UI无法相互移植，事件循环是阻碍

-   ##qmake并不是必须的

-----------

#Thank You

<br><br>
##此ppt由markdown编写，landslide开源工具生成

------------

#欢迎提问~
