# 知识随笔
  > 随便写写的，包括不限于面试题，纯文件模式 

## [网络科普](./contents/net.md)
  状态码列表，http相关

## [专业术语](./contents/term.md)
  计算机行业专用词，一些不知道怎么归类的问题，特定条件问题也会收录在这。

## [JavaScript](./contents/js.md)
  JavaScript是前端基础，框架都是基于此封装的，一定要学好哟

## [IJS](./contents/ijs.md)
  一些有趣的JavaScript知识，不一定会使用到，但是造火箭多半会遇到的；遇到即是缘分

## [TypeScript](./contents/ts.md)
  TypeScript是js的超集，进阶内容，未来主流

## [vue2](./contents/vue.md)
  vue,三大前端框架之一，vue2现有大多数开发维护项目的使用框架

## [npm](./contents/npm.md)
  Node.js 的包管理工具，用来安装各种 Node.js 的扩展。

## [实战奇招](./contents/skill.md)
  提高开发速率的一些插件及技巧。

## [git](./contents/git.md)
  一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。

## [前端体系](./contents/engineering.md)
  有关前端开发思维的一些内容：模块化规范，webpack，设计模式...

# 项目说明
  1. 文件目录结构
    - assets：图片资源目录
    - contents：知识内容目录，不同类型知识内容按文件区分
  2. 内容md文件书写格式
      ```md
        # 文件一级标题
        > 我是引用：文件整体描述和说明
        <!-- 我是空白行 -->
        ## 二级子标题（某块知识点或内容）
        ### *三级斜体子标题*（知识点详解分块，知识说明、优缺点、用法、对比...）
          1. 有序内容列表，2空格缩进
            - 无序内容二级列表，4空格缩进（2空格md格式可能不支持）
        <!-- 我是空白行 -->
        ----- 分割线
        <!-- 我是空白行 -->
        ## 下一块内容二级子标题
      ```