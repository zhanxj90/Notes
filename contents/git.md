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