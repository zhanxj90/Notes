# 开发奇技淫巧
  > 开发过程中会使用的一些巧妙方法、快捷工具及插件。帮助你快速完成造轮子的操作，实现摸鱼自由

## 插件
### *vscode插件*
  1. GitHub Repositories: 输入github远程地址，可以不用下载远程库直接访问 
  2. 
### *npm包*
  1. store.js：```npm i store```,跨浏览器的本地存储解决方案。

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