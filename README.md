## Nodemon: 检测文件变化自动重启服务；

## sequelize mysql2驱动: 检测文件变化自动重启服务；

## nrm npm源管理器；

## nvm node版本管理；

## 中间件；

## `brew services list`；本机服务列表

## `brew services stop/start XXX`（mysql） 启动服务

## homebrew版本过气 
  1. `$ rm -rf /usr/local/Homebrew/.git`
  2. `$ brew update`
  3. `$ brew config`

## Node集成数据库操作对象模型ORM（Sequelize--node第三方库）


# 需求说明，API说明

1. 查询任务列表（根据不同对条件）
2. 新建任务
3. 编辑任务
4. 删除任务
5. 修改任务状态



# 数据库初始化
  1. 创建一个数据库
  2. 使用 `sequelize cli` 初始化 项目 的 数据库 配置 信息
          `npx sequelize init `       
  3. 生成模型文件(用于创建 `表table` `npx sequelize db:migrate`)
      1. migrate文件
      2. model文件
        `npx sequelize model:generate --name Task --attributes name:string,deadline:date,desc:string`
# 项目的发布和运维
  1. pm2(自动重启) 机器全局安装...


# npm第三方包
  > `body-parser` body解析器；   
  > `multer` 文件上传；  
  > 