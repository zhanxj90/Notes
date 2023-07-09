# Git代码管理
 > 代码库增删改查管理、分支版本管理；码农必备，不会你就完了

## 常用命令
  1. git clone
  2. git branch
  3. git remote prune origin
  4. git add
  5. git status
  6. git commit
  7. git push
  8. git pull
### *git设置命令*
  1. 查看用户名，邮箱
      ```shell
      // 查看用户名
      git config user.name

      // 查看邮箱
      git config user.email
      ```
  2. 修改用户名，邮箱
      ```shell
      // 修改当前仓库的用户名和邮箱
      git config --local user.email "你的名字"
      git config --local user.email "你的邮箱"

      // 修改全局的用户名和邮箱
      git config --global user.email "你的名字"
      git config --global user.email "你的邮箱"
      ```

-----

## git通用流程规范
### *commit格式规范*
  1. commit描述内容：'$key: $description'（例：git commit -m 'init: 初始化首页'）
      - $key：提交类型关键字
      - $description：描述内容

      ```
      // 关键字枚举
      'init', // 初始化
      'feat', // 新功能(feature)
      'fix', // 修补bug
      'docs', // 文档(documentation)
      'style', // 格式、样式(不影响代码运行的变动)
      'refactor', // 重构(即不是新增功能，也不是修改BUG的代码)
      'perf', // 优化相关，比如提升性能、体验
      'test', // 添加测试
      'build', // 编译相关的修改，对项目构建或者依赖的改动
      'ci', // 持续集成修改
      'chore', // 构建过程或辅助工具的变动
      'revert', // 回滚到上一个版本
      'workflow', // 工作流改进
      'mod', // 不确定分类的修改
      'wip', // 开发中
      'types', // 类型修改
      'release', // 版本发布
      ```

-----

## github管理
### *github clone*
  1. https方式链接服务器
      - 需要用户名+密码进行身份验证（密码已被访问令牌替代）
      - 用户名和令牌可以保存在window凭据中，这样不用每次都需要登录
      - 两种类型的 personal access token（令牌）
        * fine-grained personal access token：细粒度个人访问令牌；更安全，详细权限需要勾选；但是只能用于单个用户或者组织
        * Personal access tokens (classic) ：个人访问令牌（经典）；不太安全，无需详细配置；可用于多个用户或者组织（即用户和用户管理的组织 下的库都可用clone）
  2. ssh方式链接服务器
      - 本地生成ssh,生成的内容保存在```C:/Users/<username>/.ssh```
          ```shell
          $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
          ```
      - 把.ssh下的id_rsa.pub (公钥)添加到github上