# 开发奇技淫巧
  > 开发过程中会使用的一些巧妙方法、快捷工具及插件。帮助你快速完成造轮子的操作，实现摸鱼自由

## 插件
### *vscode插件*
  1. GitHub Repositories: 输入github远程地址，可以不用下载远程库直接访问 
  2. Indent-Rainbow: 自动标红没有对齐的代码行
  3. Trailing Spaces: 高亮提示代码中冗余的空格，并可以快速删掉
  4. Bracket Pair Colorizer: 为代码中的各种结对的括号提供了颜色高亮等功能，增强代码阅读性
### *npm包*
  1. store.js：```npm i store```,跨浏览器的本地存储解决方案。
### *webpack插件*
  1. clean-webpack-plugin:快速更新打包文件，自动清除打包目录旧文件
  2. html-webpack-plugin:自动生成html文件
  3. copy-webpack-plugin:复制静态文件到打包目录里
### *webpack内置插件*
  1. DefinePlugin：全局常量定义
    ```js
      const webpack=require('webpack')
      module.exports={
        plugins:{
          new webpack.DefinePlugin({
            // 常量值必须是字符串，字符串的值就是常量的值
            PI:`Math.PI`
            DOMAIN:JSON.stringify("baidu.com")
          })
        }
      }
    ```

-----

## node相关
### *nvm切换高版本，npm i报错*
  1. 出现时机：当通过nvm切换nodejs版本为16以上时
  2. 报错内容：`npm install [package]`报错：`Unexpected token '.'`
  3. 问题原因：该问题不是npm的问题，也不是nodejs的问题，是nvm-windows的问题。
  4. 解决方案：
      - 1：前往github重新下载高版本nvm-windows（1.1.9以下可能会报错，1.1.9以上ok）
      - 2：前往github下载[nvm-update.zip更新包](https://github.com/coreybutler/nvm-windows/releases),然后本地直接解压，双击运行选择更新的版本即可（快速方案）
  5. 注意事项：出现问题的nodejs版本需要uninstall重装
### *ndoe版本切换问题*
  1. 出现时机：当使用 nvm use 命令切换版本
  2. 报错内容：乱码显示`exit status 1 xxxxxx`
  3. 问题原因：没有权限操作，控制台权限不够
  4. 解决方案：
      - 1：使用管理员运行（win10 系统可以右键 win 图标, 选择 "Windows PowerShell(管理员)）
      - 2：nvm安装目录下打开控制台，输入`Set-ExecutionPolicy -Scope CurrentUser`,确认后输入`remotesigned`（可能也不起作用）
      - 3：切换至1.1.10以上版本（快速方案）

-----

## 调试技巧
## 企微打开控制台
  1. 下载 devtools_resources.pak 放到企业微信的WXWork目录下（这个可能不要也行）
  2. control+command+shift+d （window：ctrl+alt+shift+d）
### 本地调试企微开发
  1. 开发场景：开发企微应用，或者需要在企微浏览器运行的项目（微信环境下可能也通用）
  2. 解决问题：因为需要企微环境的支持，所以项目部署时无法使用本地环境调试，需要测试环境或线上环境，这样每次调试问题都需要发布打包很麻烦
  3. 解决方案：
      - 修改hosts文件,配置本地与服务器地址的映射
      - 本地运行代码，必须是80端口
  4. 可能会出现的报错：
      - hosts文件修改不了：文件权限不够;Mac上打开文件简介，修改用户权限为读写，还不行接着修改上级目录的权限；window在属性中更改文件所有者
      - 页面打开白屏：可能页面打开时有重定向到企微域名过，域名代理被拦截了，控制台输入location.href='当前域名'跳转一下
