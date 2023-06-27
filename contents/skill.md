# 开发奇技淫巧
  > 开发过程中会使用的一些巧妙方法、快捷工具及插件。帮助你快速完成造轮子的操作，实现摸鱼自由

## 插件
### *vscode插件*
  1. GitHub Repositories: 输入github远程地址，可以不用下载远程库直接访问 
  2. 

-----

## node相关
### *nvm切换高版本，npm i报错*
  1. 出现时机：当通过nvm切换nodejs版本为16以上时
  2. 报错内容：`npm install [package]`报错：`Unexpected token '.'`
  3. 问题原因：该问题不是npm的问题，也不是nodejs的问题，是nvm-windows的问题。
  4. 解决方案：
      - 1：前往github重新下载高版本nvm-windows（1.1.7会报错，1.1.8没试过不知道，1.1.9以上ok）
      - 2：前往github下载[nvm-update.zip更新包](https://github.com/coreybutler/nvm-windows/releases),然后本地直接解压，双击运行选择更新的版本即可（快速方案）
  5. 注意事项：出现问题的nodejs版本需要uninstall重装