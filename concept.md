# 重要概念

## Monibuca实例

- 所谓实例，就是一个Go项目，该项目的主程序将会引入Monibuca引擎，并启动引擎。
- 实例两种创建方式：
1. 使用monica实例管理器创建
2. 手工创建。
### 问：为什么不直接启动Monibuca，而是需要创建一个实例来运行？

答：由于Monibuca被设计成使用插件方式运行，所以每一个Monibuca的程序都是定制化，所以需要有一个自动化搭建项目目录的过程，完成定制化项目的创建。这样做的好处如下
1. Monibuca启动和业务程序的启动解耦
2. 一台机器可以很容易运行多个不同功能的实例程序
3. 方便Monibuca独立升级
4. 方便开发进程管理程序

## StreamPath

拉流时需要填写StreamPath，这个StreamPath就是房间的唯一标识。举例：
- FFmpeg推流时rtmp://127.0.0.1/live/stream1 其中live/stream1就是StreamPath
- 同理，如果用jessibuca播放ws://127.0.0.1/live/stream1，也是一样。
- 用hdl拉流即：http://127.0.0.1/live/stream1.flv
- 如果用OBS推流，地址填写rtmp://127.0.0.1/live，流密钥 stream1，最终也是一样。

> 在rtmp协议中live称为app名称 stream1称为流名称。
> 在flash中播放rtmp流需要先使用NetConnection连接rtmp://127.0.0.1/live，然后再使用NetStream 的Play("stream1")来进行播放

## Stream状态

### 当配置文件中的EnableWaitStream = true时

Stream一共两种状态：
1. 等待
2. 正在发布

- 当订阅者订阅了某个Stream，但是该Stream并不存在时，会创建Stream并进入等待状态。
- 当发布者创建了Stream，那么该Stream会直接进入正在发布的状态。

### 当配置文件中的EnableWaitStream = false时

Stream只有一种状态：正在发布

### 销毁Stream的条件：
1. 发布者离开Stream
2. Stream处于等待状态，最后一个订阅者离开Stream

## 推流

- 由发布端主动向流媒体服务器推送音视频的行为成为推流。
- 常见的推流方式就是通过OBS，FFmpeg或者其他推流器采集视频后将编码过的数据传递到流媒体服务器中去。
- 这个过程中，推流端需要提前知道服务器的IP地址和暴露的端口信息，还需要提供推流的流ID给服务器。
- 直播软件APP都会内置推流程序，将手机采集的视频和音频发送到服务器端。
- 最流行的推流协议就是rtmp协议

## 拉流

- 拉流有两种场景，一种是播放器端播放直播流的行为，这个比较常见。
- 还有一种比较重要的拉流场景，就是服务器A端向另外的服务器B请求流媒体的过程，此时服务器A充当播放器的角色，获取到流媒体以后再转发给它自己的客户端。
- 常见的拉流协议有rtmp、rtsp、http-flv、websocket、hls等
- 在Monibuca的插件中，如果能够从其他地方拉流获取数据的插件都称为发布者，包括接收推流器推流的插件、从源服务器拉流的集群插件，从其他服务器拉流的插件。
- 拉流需要提前知道流媒体源的地址和流ID等信息。

## 和ffmpeg的区别

ffmpeg可以推流也可以拉流，实现拉流转发。流媒体服务器与ffmpeg的最大区别是，流媒体服务器提供被动连接的服务，可以实现一个媒体源向多个播放器传输媒体的功能。而ffmpeg无法提供被动连接，只能主动向流媒体服务器发起拉流或者推流的连接。