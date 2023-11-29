# UDPNetwork 基于Unity提供的UDP通信和同步服务
# API提供一下类的实现：

1.UDPServer服务器实现；

2.UDPClient实现；

3.可靠UDP实现；

4.可靠UDP消息互发机制的通讯实现；

5.UDP状态同步的实现。

# API介绍：

1.开启服务器
开启服务器使用UDPServer类，用法如下：
UDPServer server = new UDPServer(serverName, ip, serverLocalPort, serverListenPort, syncThreadNum);
参数含义：
serverName：服务器名字；ip：监听客户端的ip地址；serverLocalPort：服务器用于监听客户端连接的端口；serverListenPort：服务器用于监听客户端消息的端口；syncThreadNum：用于状态同步的线程数；

2.启动客户端
UDPClient client = new UDPClient(clientName, localIp, clientLocalPort, serverHost, serverPort, syncThreadNum);
参数含义：
clientName:客户端名字；localIp：客户端用于监听消息的ip地址；clientLocalPort：客户端用于监听消息的端口号；serverHost：服务器的地址；serverPort：服务器的端口（serverLocalPort）；syncThreadNum：用于状态同步的线程数；

3.状态同步的使用

SynContainer类提供同步服务，用法如下：

(1)服务器开启同步：SynContainer synContainer = new SynContainer(syncName, udpServer);
参数含义：

syncName：同步的名字;

udpServer:UDPServer对象；

(2)客户端开启同步：SynContainer synContainer = new SynContainer(syncName, udpClient);
参数含义：

syncName：同步的名字;

udpClient:UDPClient对象；

(3)属性、字段、Action的含义：

SyncInterval：执行OnTransportPush同步的间隔

PlayTime：播放时间, 預留字段

Issue：網絡物體的主控制权

IsGZip：是否使用GZip压缩消息数据

IsAllowsDuplicates:是否允许重复的消息数据(byte比较)

OnUpdate：每帧调用

OnMessagePush:收到同步消息的时候调用

OnTransportPush：开始同步消息的时候调用（推送消息）

ClientOnMessagePush:收到客户端消息的时候调用（被推送消息）

ServerOnMessagePush:收到服务器消息的时候调用（被推送消息）

ClientOnTransportPush:客户端开始同步消息的时候调用（推送消息）

ServerOnTransportPush：服务器同步消息的时候调用（推送消息）

(4)方法的用法

OnCastMessage（byte[] ByteArray）:无区别广播消息

Pop（）：返回object对象，用于在ClientOnMessagePush、ServerOnMessagePush、OnMessagePush的时候调用，返回推送过来的数据，需要自己转换成对应的类型；

Push（EmPushType PushType, object Value）:推送消息，用于在ClientOnTransportPush、ServerOnTransportPush中使用，参数：PushType，推入的参数类型，可以为以下类型：

Int32、Int64、Long、Float、Double、String、Bool、Byte、ByteArray。object，要推送的参数值；

PushByte（byte Value）:推送消息，用于在ClientOnTransportPush、ServerOnTransportPush中使用,参数只能是byte。

4.可靠UDP消息收发

使用RpcItemSync类进行消息量化处理，用法：
RpcItemSync item = new RpcItemSync(messageName, id, syncId, udpServer, argLen, (syncId, args) =>{});
udpServer.Register(id, new INetworkSync[] { item });
此操作之后，在服务器可以发送和收到消息名字为messageName、id、syncId的消息，客户端注册消息与此类似；
