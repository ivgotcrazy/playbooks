IceGrid 节点信息如下：
1. registry（管理节点）：共两个，分别为 master/replica
2. node（工作节点）：共两个，分别为 node1/node2

IceGrid 拓扑信息如下：
节点名字        IP              监听端口        备注
master          192.168.1.1     10000
replca          192.168.1.2     10001
node1           192.168.1.3     30000           可部署在 192.168.1.1 机器
node2           192.168.1.4     30000           可部署在 192.168.1.2 机器

需要修改的配置信息：
（！！！建议不要更改节点名字，如果更改，需要保证与 application.xml 一致。）
1. master 只需要指定监听端口 10000
2. replca 只需要配置 master 的 IP、端口，以及自身监听的端口 10001
3. node1  只需要配置 master/replica 的 IP、端口，以及 Redis 的 IP、端口
   运行 chown -R nobody log 或者 chmod 777 log
4. node2  只需要配置 master/replica 的 IP、端口，以及 Redis 的 IP、端口
   运行 chown -R nobody log 或者 chmod 777 log
5. register_app 只需要配置 master/replica 的 IP、端口

备注：
1. 上面 node1/node2 之所以提到监听端口，因为线上环境需要根据端口制定防火墙策略，
所以固定端口为 30000。
2. 该端口 30000 是 ice_proxy 访问从管理节点 master/replica 处分配到的 ice_server
所需要的，即，30000 是 ice_proxy 与 ice_server 交互使用的。
3. 就 IceGrid 机制来说，可以缺省不指定端口，但是将会随机生成端口，无法满足防火墙
策略。

启动步骤：
1. 部署 master，启动 icegridregistry --Ice.Config=config.master -- 安装包目录 ice_server/master
2. 部署 replica，启动 icegridregistry --Ice.Config=config.replica -- 安装包目录 ice_server/replica
3. 部署 node1，启动 icegridnode --Ice.Config=config.server -- 安装包目录 ice_server/node1
4. 部署 node2，启动 icegridnode --Ice.Config=config.server -- 安装包目录 ice_server/node2
5. 注册应用 icegridadmin --Ice.Config=config.client -e "application add 'application.xml'" -- 安装包目录 ice_server/register_app
（如果注册失败，提示应用已注册，先执行 icegridadmin --Ice.Config=config.client -e "application remove 'DBApp'"
然后重复步骤 5 即可）
