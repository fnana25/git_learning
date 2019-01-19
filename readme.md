# 开发准
#### 安装idea i 
#### 安装maven
#### 安装git （安装smartGit 客户端）
#### 安装idea插件lombok
#### 修改idea启动文件目录下的 
    你启动的idea.exe.vmoptions 加入
    -Dide.run.dashboard=true
#### 在项目文件夹
    根据环境启动 build.sh/build.bat   选择 初始化


# 开发规范
   ##### 编码规范，参考阿里巴巴 JAVA 开发手册
## 平台 URL 规范

### 前端 URL 规范

前端 URL 由 以下三部分组成

```
http(s)://平台相关根域名/业务中文拼音/具体的接口路由
```

### REST 规范1

1. API 命名方式遵循 RESTful 的部分通用规则

```
   参照Ruby On Rails中提供的命名和规范，见下表：

   HTTP Verb	Path	Controller#Action	Used for
   GET	/photos	photos#index	display a list of all photos
   POST	/photos	photos#create	create a new photo
   GET	/photos/:id	photos#show	display a specific photo
   GET	/photos/:id/edit	photos#editForm	return an HTML form for editing a photo
   PATCH/PUT	/photos/:id	photos#update	update a specific photo
   DELETE	/photos/:id	photos#destroy	delete a specific photo
```
  
   按照我们自己项目的实际情况，约定：
   
  - 绝大多数的方法都走 `POST` 方法

  - 使用名词, 复数进行资源命名, 不管资源返回单个还是多个
    
    ```
    POST   https://www.jiabangou.com/api/users 
    ```
    
   

    
## controller 层接口返回参数规范

1. 单个实体对象，如pojo对象、map对象等直接输出，不需要 `Result` 封装, 直接返回对象

   如：`return user`

   
1. 如果有需要，controller 层应该使用 `VO` 方式，过滤掉 service 层返回的 DTO 对象属性。   

## service 层接口封装规范
   
1. 接口是**有状态**的（如需登录，授权，验签等）需要封装传入应用的上下文

1. 如传入参数字段过多，应当封装成一个对象传入

1. service层接口返回**不需要**用Result封装，

1. 所有的model，pojo对象都要加上 implements Serializable，以保证分布式时序列化；toString方法也需要实现

1. 所有 service 层的 DTO 对象命名不带 DTO，所有的 DTO 对象写到 `models` 包下， 如 `UserDTO` 现在只需要为 `User`即可。

1. service 层命名规范

   - 查询结果为单个对象，方法名称以 `get` 打头，禁止用 `find obtain` 打头。
   
     ```
     User getByMobileOrName(final String mobile, final String userName);
     User getById(final Long userId);
     ```
     
   - 查询结果为列表，方法名称以 `list` 打头
     
     ```
     List<User> listByIds(final List<Long> userIds);
    
     ```
   - 查询结果为分页，方法名称以 `page` 打头
     ```
         Page<User> pageByIds(final String name, final String gender, final String mobile, final Integer offset, final Integer pageSize);

     ```
   - 新增对象，方法名称以 `save` 打头, 禁止用 `create new` 打头，按照需要，按照需要决定是否返回创建成功后的对象。
   
     ```
     User save(final User user);
     User save(final UserCreateInfo userCreateInfo);    
     ```
   
   - 更新对象，方法名称以 `update` 打头, 禁止用 `modify adjust` 打头，按照需要决定是否返回更新后的对象  
   
     ```
     User update(final User user); 
     ```
   - 删除对象，方法名称以 `remove`,  禁止用 `delete` 打头 打头
   
     ```
     void remove(final User user);
     void remove(final Long userId);
     Boolean remove(final Long userId);
     ```
   - 统计类方法以 `count` 打头
   
     ```
     Intger countByMobiles(final List<String> mobiles);
     ```
     
   - 其它业务类方法，确实无法使用上述方法的情况下，可以自行命名  
   

## dao 层接口规范

1. 所有 dao 接口以 `XxxRepository` 方式命名， 所有实体命名以 `XxxEntity` 命名


#
#
# 分支开发流程
### 正常业务开发

    1.(先pull)拉一个feature 
        取名规则 开发内容+当前日期
    2.开发完成测试无重大bug 后 finish feature 
    3.拉取release  
        取名规则 测试内容+当前日期
    4.finish release 并生成tag 
        取名规则 版本号+.REKEASE

### 线上bug解决开发

    1.(先pull)拉一个hotfixes
        取名规则 bug内容+当前日期
    2.finish hotfixes 并生成tag 
        取名规则 版本号+.REKEASE
    



# 
# 
# 发布与维护

## 环境准备：

  - 安装docker 并添加加速

        sudo mkdir -p /etc/docker
        sudo tee /etc/docker/daemon.json <<-'EOF'
        {
          "registry-mirrors": ["https://ggz7w2an.mirror.aliyuncs.com"]
        }
        EOF
        sudo systemctl daemon-reload
        sudo systemctl restart docker
        
  - 安装tengine(http://files.chongkouwei.com/nginx/install_nginx_http2.sh)
        
        (上面脚本已做)
        软链到 /home/admin/nginx
            ln -s tengine原始目录 /home/admin/nginx
        在nginx目录执行代码
            sudo chown -R admin:admin logs
            sudo chown -R admin:admin conf
        在sbin目录执行
            chmod ug+s nginx
        修改nginx.conf
            在http下加入   include       /root/wbd-deploy/nginx/*.conf;
        启动nginx
        
## 发布：

    1.进入发布目录
    2.发布命令
        2.1 单项目预定义发布
            执行命令             node ./deploy.js profile module branch
            如发布common的测试   node ./deploy.js test wbd-common-service master
        2.2 可视化发布
            执行命令 node m2-deploy.js  根据提示进行操作
   

## 查看日志：
    1.进入docker 查看
        1.1.进入相应的服务器
        1.2.执行 docker ps -a 查看要需要的容器的id
        1.3.执行 docker exec -it 容器的id /bin/bash 
        1.4.进入 logs 查看日志
    2.宿主机查看
        2.1.进入相应的服务器
        2.2.进入 wbd-deploy 下的 deploy 的相应项目下logs目录
        2.3.进入 logs 查看日志

## 容器的启动和关闭

    1.关闭 docker stop 容器的id
    2.启动 docker start 容器的id

## 维护相关：

    1.镜像维护
        1.1.查看全部镜像 docker images
        1.2.删除没有使用的镜像(名称带none)  docker rmi $(docker images | grep "none" | awk '{print $3}')
    2.容器维护
        2.1.查看全部容器 docker ps -a
        2.2.删除没有使用的容器  docker rm $(docker ps -a | grep "Exited" | awk '{print $1}')





