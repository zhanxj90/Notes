# NPM
  > 先模仿后超越，不要只会使用npm，也要会开发npm。该文件讲述了常用npm包，及npm包开发相关

## npm开发
### *开发流程*
  1. 按正常开发项目的流程写一个工具库或项目框架，需注意的是要通过main.js导出需要使用的内容
  2. 注册npm账号，并在本地终端登录
      - [npmjs](https://www.npmjs.com/)上注册账号
      - 终端输入`npm adduser`
      - 输入用户名，密码，邮箱登陆账号
      - 登陆成功输入`npm whoami`验证,显示用户名则成功
  3. 发布npm包，成功之后npm i就可以下载自己的包了
      ```cmd
      npm publish
      ```
### *npm项目目录结构*
  1. README：介绍文件，代码库首页会显示，npm官网不显示
  2. gitignore：忽略文件
  3. node_modules：依赖包目录
  4. main.js：导出出口文件，必须要有的
  5. package.json：包配置文件，必须字段（name--包名；version--版本号）
### *注意事项*
  1. npm源要是npm官网的；淘宝镜像无法登陆；公司内部镜像登陆的是公司的，外部可能无法访问
  2. 终端登录时，输入邮箱后可能会需要验证码，npm会发到输入的邮箱里
  3. 包名必须唯一，否则发布不了；加了@组织前缀的，要用`npm publish --access public`发布
### *发布常见报错*
  1. 邮箱未验证
      - 错误码：403
      - 关键词：you must verify your email
      - 注册npm账号时未验证邮箱，登陆邮箱确认验证就行，过期了去npm主页重新发个验证就行
  2. 包重名
      - 错误码：403
      - 关键词：You do not have permission 
      - 包重名了，修改名字就行，npm上搜索一下，没有就是没人用
  3. 需要登录
      - 错误码：ENEEDAUTH
      - 关键词：You need to authorize
      - 切换了npm源或者登陆过期了，重新登陆就行
  4. 没有发布权限
      - 错误码：403
      - 关键词：only admin can publish
      - npm源用的是第三方源，比如设置了淘宝源，改回默认源
  5. 包名过于类似
      - 错误码：403
      - 关键词：Package name too similar to existing package
      - 修改包名，或者加上自己的组织或用户名前缀
  6. 无法发布到私有包
      - 错误码：402
      - 关键词：must sign up for private package
      - 包名加了用户名称或者组织做前缀才出现的，用`npm publish --access public`发布
  7. npm源安全域名报错
      - 错误码：426
      - 关键词：ERR! 426 Upgrade Required
      - npm网站和npm registry必须使用TLS协议，npm源必须是https协议的

-----

## 公开作用域包
  > 以@开头的包，即公开的带有作用域带的npm包（public scoped package）
### *发布作用域包*
  1. 作用域包命名：@<your_user_name>/package_name 或 @<your_org_name>/package_name
      ```json
      {
          "name":"@zhanxj/tools"
      }
      ```
  2. 作用域包发布：`npm publish --access public`
### *作用域包分类*
  1. 免费的公开作用域包分两种:
      - 以用户为作用域的包（public user scoped package）
      - 以组织为作用域的包（public organization scoped package）

-----
