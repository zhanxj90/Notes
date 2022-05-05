# 前端兼容处理
  > 虽然你不一定用的上，一些低版本浏览器甚至都不存在了，但是这个知识还是需要了解的


## html兼容

### 时间格式兼容
  - iso系统上不支持-连接符格式的时间字符串（eg：YYYY-MM-DD）；这类字符串转换成时间显示‘Invalid Date’


## css兼容

### 表单样式
  - autofocus：支持的浏览器有 Firefox 4+、Safari 5+、Chrome 和 Opera 
  9.6；不支持的可以使用focus方法通过js获取


## js兼容

### 表单方法
  - 选择全部文本：
    ``` js
    // textbox-要操作的文本框
    function getSelectedText(textbox){ 
      if (typeof textbox.selectionStart == "number"){ 
        return textbox.value.substring(textbox.selectionStart,textbox.selectionEnd); 
      } else if (document.selection){ 
        // IE8 及之前版本
        return document.selection.createRange().text; 
      } 
    }
    ```
  - 选择部分文本：
    ``` js
    // textbox-要操作的文本框；startIndex-开始字符索引（0开始）；stopIndex-结束字符索引
    function selectText(textbox, startIndex, stopIndex){ 
      if (textbox.setSelectionRange){ 
        textbox.setSelectionRange(startIndex, stopIndex); 
      } else if (textbox.createTextRange){ 
        // IE8 及之前版本
        var range = textbox.createTextRange(); 
        range.collapse(true); 
        range.moveStart("character", startIndex); 
        range.moveEnd("character", stopIndex - startIndex); 
        range.select(); 
      } 
      textbox.focus();
    }
    ```

### ajax技术
  - 创建XMLHttpRequest对象
  ``` js
  function createXHR(){ 
    if (typeof XMLHttpRequest != "undefined"){ 
      return new XMLHttpRequest(); 
    } else if (typeof ActiveXObject != "undefined"){ 
      //适用于 IE7 之前的版本
      if (typeof arguments.callee.activeXString != "string"){ 
        //通常情况下这个ActiveXObject版本检测也不会使用，直接就是new ActiveXObject（）
        var versions = [ "MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", 
        "MSXML2.XMLHttp"], 
        i, len; 
        for (i=0,len=versions.length; i < len; i++){ 
          try { 
            new ActiveXObject(versions[i]); 
            arguments.callee.activeXString = versions[i]; 
            break; 
          } catch (ex){ 
            //跳过
          } 
        } 
      } 
      return new ActiveXObject(arguments.callee.activeXString); 
    } else { 
      throw new Error("No XHR object available."); 
    } 
  }
  ```
  - CORS处理
  ``` js
  function createCORSRequest(method, url){ 
    var xhr = new XMLHttpRequest(); 
    if ("withCredentials" in xhr){ 
      // 支持XMLHttpRequest
      xhr.open(method, url, true); 
    } else if (typeof XDomainRequest != "undefined"){ 
      // 支持XDomainRequest；IE 中的
      xhr = new XDomainRequest(); 
      xhr.open(method, url); 
    } else { 
      xhr = null; 
    } 
    return xhr; 
  }
  ```
