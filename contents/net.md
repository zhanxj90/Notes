# 网络相关（关于网上的那些事）

## 小鲁班（HTTP）和凯爹（HTTPS）
### *http请求过程*
  1. 域名解析
  2. 与服务器建立连接
  3. 发起HTTP请求
  4. 服务器响应HTTP请求，处理请求
  5. 访问资源
  6. 构建响应报文
  7. 发送响应报文 
  8. 记录日志
### *HTTPS*
### *HTTP跟HTTPS的不同*
  1. 安全性不同：HTTPS是HTTP经过SSL/TLS加密的
  2. 网站申请流程不同：https需要SSL证书，一般要购买的
  3. 默认端口不同：http是80，https是443
  4. 对搜索排名的提升：https更高，是重要权重指标
  5. http无状态，无连接
  6. HTTPS连接缓存不如HTTP高效，流量成本高。

-----

## 浏览器缓存机制
### *缓存执行流程*
  1. 首先验证强缓存是否可用
  2. 如果强缓存可用，直接使用
  3. 否则进入协商缓存，即发送 HTTP 请求，服务器通过请求头中的 If-Modified-Since 或者If-None-Match 这些条件请求字段检查资源是否更新
  4. 若资源更新，返回资源和 200 状态码
  5. 否则，返回 304，告诉浏览器直接从缓存获取资源
  6. ps:第一次可能没有缓存，第二次一定会有了（因为第一次响应会返回协商缓存）
### *强缓存*
### *http状态码*
  1. 1xx: 表示目前是协议处理的中间状态，还需要后续操作。
  2. 2xx: 表示成功状态。
  3. 3xx: 重定向状态，资源位置发生变动，需要重新请求。
  4. 4xx: 请求报文有误。
  5. 5xx: 服务器端发生错误

-----

## 浏览器渲染
### *浏览器的进程模型*
  1. 浏览器是一个多进程多线程的应用程序（线程依赖于进程的创建）
  2. 为了避免相互影响，为了减少连环崩溃的几率，当启动浏览器后，它会自动启动多个进程。
  3. 浏览器最主要的进程有：
      - 浏览器进程：主要负责界面显示、用户交互、子进程管理等。浏览器进程内部会启动多个线程处理不同的任务。
      - 网络进程：负责加载网络资源。网络进程内部会启动多个线程来处理不同的网络任务。
      - 渲染进程：渲染html页面，执行前端代码
### *进程*
  1. 定义：程序运行需要有它自己专属的内存空间，可以把这块内存空间简单的理解为进程![](/assets/202208092057573.png)
  2. 每个应用至少有一个进程，进程之间相互独立，即使要通信，也需要双方同意。
### *线程*
  1. 有了进程后，就可以运行程序的代码了。运行代码的「人」称之为「线程」。
  2. 一个进程至少有一个线程，所以在进程开启后会自动创建一个线程来运行代码，该线程称之为主线程。
  3. 如果程序需要同时执行多块代码，主线程就会启动更多的线程来执行代码，所以一个进程中可以包含多个线程。![](/assets/202208092108499.png)
### *渲染进程*
  1. 渲染页面的进程，前端开发主要接触的进程
  2. 默认情况下，浏览器会为每个标签页开启一个新的渲染进程，以保证不同的标签页之间不相互影响。将来该默认模式可能会有所改变，有兴趣的同学可参见[chrome官方说明文档](https://chromium.googlesource.com/chromium/src/+/main/docs/process_model_and_site_isolation.md#Modes-and-Availability)
  3. 渲染进程启动后，会开启一个**渲染主线程**，主线程负责执行 HTML、CSS、JS 代码。
  4. 关于渲染进程的两种说法：
      - 非谷歌浏览器：渲染和js分开，GUI渲染线程和JS引擎线程互斥进行
      - 谷歌浏览器：渲染主线程执行渲染和js
  5. 渲染进程的主要线程：
      - 渲染主线程:相当于GUI渲染线程+JS引擎线程，两者互斥，内部可能是两者交替进行的(谷歌浏览器)
      - GUI渲染线程：负责渲染页面，解析html和CSS、构建DOM树、CSSOM树、渲染树、和绘制页面，重绘重排也是在该线程执行。在Javascript引擎运行脚本期间,GUI渲染线程都是处于挂起状态的，GUI更新会被保存在一个队列中等到引擎线程空闲时立即被执行(非谷歌浏览器)
      - JS引擎线程：一个页面只有一个JS引擎(单线程)，负责解析和执行js，处理用户交互，操作DOM树和CSS样式树。(非谷歌浏览器)
      - 合成线程:渲染进程在渲染页面的绘制步骤完成启用**合成线程**完成分块
      - HTTP线程：如果请求有回调函数，则把回调函数添加到事件队列，排队等待JS引擎处理
      - 定时器线程：因为JS引擎是单线程，如果阻塞定时器线程就不准确了，所以浏览器开辟了单独的线程来负责定时器线程。
      - DOM线程：主要用来控制事件循环，当一个事件被触发时该线程会把事件添加到待处理队列的队尾，排队等待JS引擎处理
### *渲染主线程（谷歌浏览器）*
  1. 渲染主线程是浏览器中最繁忙的线程，需要它处理的任务包括但不限于：
      - 解析 HTML
      - 解析 CSS
      - 计算样式
      - 布局
      - 处理图层
      - 每秒把页面画 60 次
      - 执行全局 JS 代码
      - 执行事件处理函数
      - 执行计时器的回调函数
      - ......
  2. 因要执行多个任务，为解决任务调度的问题，渲染主线程使用排队的方式进行，也就是消息循环
### *浏览器渲染页面流程*
  1. 当浏览器的网络线程收到 HTML 文档后，会产生一个渲染任务，并将其传递给渲染主线程的消息队列。
  2. 在事件循环机制的作用下，渲染主线程取出消息队列中的渲染任务，开启渲染流程。
  3. 整个渲染流程分为多个阶段，分别是： HTML 解析、样式计算、布局、分层、绘制、分块、光栅化、画
  4. 每个阶段都有明确的输入输出，上一个阶段的输出会成为下一个阶段的输入。
  5. 这样，整个渲染流程就形成了一套组织严密的生产流水线。
### *渲染步骤分解*
  1. 解析 HTML
      - 解析过程中遇到 CSS 解析 CSS，遇到 JS 执行 JS。为了提高解析效率，浏览器在开始解析前，会启动一个预解析的线程，率先下载 HTML 中的外部 CSS 文件和 外部的 JS 文件。
      - 如果主线程解析到`link`位置，此时外部的 CSS 文件还没有下载解析好，主线程不会等待，继续解析后续的 HTML。这是因为下载和解析 CSS 的工作是在预解析线程中进行的。这就是 CSS 不会阻塞 HTML 解析的根本原因。
      - 如果主线程解析到`script`位置，会停止解析 HTML，转而等待 JS 文件下载好，并将全局代码解析执行完成后，才能继续解析 HTML。这是因为 JS 代码的执行过程可能会修改当前的 DOM 树，所以 DOM 树的生成必须暂停。这就是 JS 会阻塞 HTML 解析的根本原因。
      - 完成后，会得到 DOM 树和 CSSOM 树，浏览器的默认样式、内部样式、外部样式、行内样式均会包含在 CSSOM 树中。
  2. 样式计算
      - 主线程会遍历得到的 DOM 树，依次为树中的每个节点计算出它最终的样式，称之为 Computed Style。
      - 在这一过程中，很多预设值会变成绝对值，比如`red`会变成`rgb(255,0,0)`；相对单位会变成绝对单位，比如`em`会变成`px`
      - 完成后，会得到一棵带有样式的 DOM 树。
  3. 布局
      - 布局阶段会依次遍历 DOM 树的每一个节点，计算每个节点的几何信息。例如节点的宽高、相对包含块的位置。
      - 大部分时候，DOM 树和布局树并非一一对应;比如`display:none`的节点没有几何信息，因此不会生成到布局树；又比如使用了伪元素选择器，虽然 DOM 树中不存在这些伪元素节点，但它们拥有几何信息，所以会生成到布局树中。还有匿名行盒、匿名块盒等等都会导致 DOM 树和布局树无法一一对应。
  4. 分层
      - 主线程会使用一套复杂的策略对整个布局树中进行分层。
      - 分层的好处在于，将来某一个层改变后，仅会对该层进行后续处理，从而提升效率。
      - 滚动条、堆叠上下文、transform、opacity 等样式都会或多或少的影响分层结果，也可以通过`will-change`属性更大程度的影响分层结果。
  5. 绘制
      - 主线程会为每个层单独产生绘制指令集，用于描述这一层的内容该如何画出来。
  6. 分块
      - 完成绘制后，主线程将每个图层的绘制信息提交给合成线程，剩余工作将由合成线程完成。
      - 合成线程首先对每个图层进行分块，将其划分为更多的小区域。
      - 它会从线程池中拿取多个线程来完成分块工作。
  7. 光栅化
      - 合成线程会将块信息交给 GPU 进程，以极高的速度完成光栅化。
      - GPU 进程会开启多个线程来完成光栅化，并且优先处理靠近视口区域的块。
      - 光栅化的结果，就是一块一块的位图
  8. 画
      - 合成线程拿到每个层、每个块的位图后，生成一个个「指引（quad）」信息。
      - 指引会标识出每个位图应该画到屏幕的哪个位置，以及会考虑到旋转、缩放等变形。
      - 变形发生在合成线程，与渲染主线程无关，这就是`transform`效率高的本质原因。
      - 合成线程会把 quad 提交给 GPU 进程，由 GPU 进程产生系统调用，提交给 GPU 硬件，完成最终的屏幕成像。
### *事件循环*
  1. 事件循环又称之为消息循环,是浏览器渲染主线程的工作方式。
  2. 执行步骤：
      - 在最开始的时候，渲染主线程会进入一个无限循环，一个不会结束的 for 循环
      - 每一次循环会检查消息队列中是否有任务存在。如果有，就取出第一个任务执行，执行完一个后进入下一次循环；如果没有，则进入休眠状态。
      - 其他所有线程（包括其他进程的线程）可以随时向消息队列添加任务。新任务会加到消息队列的末尾。在添加新任务时，如果主线程是休眠状态，则会将其唤醒以继续循环拿取任务
  3. 任务没有优先级，在消息队列中先进先出，但**消息队列是有优先级的**
  4. 任务定义说明：
      - 每个任务都有一个任务类型，同一个类型的任务必须在一个队列，不同类型的任务可以分属于不同的队列。
      - 在一次事件循环中，浏览器可以根据实际情况从不同的队列中取出任务执行。
      - 浏览器必须准备好一个微队列，微队列中的任务优先所有其他任务执行
  5. 之前的任务队列定义：
      - 宏队列：用来保存执行的宏任务（回调），比如：dom事件回调，ajax回调，定时器回调
      - 微队列：用来保存执行的微任务（回调），比如：promise回调，mutation回调
  6. 随着浏览器的复杂度急剧提升，W3C 不再使用宏队列的说法,在目前 chrome 的实现中，至少包含了下面的队列：
      - 延时队列：用于存放计时器到达后的回调任务，优先级「中」
      - 交互队列：用于存放用户操作后产生的事件处理任务，优先级「高」
      - 微队列：用户存放需要最快执行的任务，优先级「最高」,添加任务到微队列的主要方式主要是使用 Promise、MutationObserver
### *如何理解 JS 的异步*
  1. 何为异步？
      - JS是一门单线程的语言，这是因为它运行在浏览器的渲染主线程中，而渲染主线程只有一个。
      - 代码在执行过程中，会遇到一些无法立即处理的任务，比如：定时器，网络请求，用户操作。。。
      - 如果使用同步的方式，让渲染主线程等待这些任务的时机达到，会导致消息队列中的很多其他任务无法得到执行。
      - 就会导致主线程长期处于「阻塞」的状态，一方面会导致繁忙的主线程白白的消耗时间，另一方面导致页面无法及时更新，从而导致浏览器「卡死」
      - 而渲染主线程承担着极其重要的工作，渲染页面、执行 JS 都在其中运行，无论如何都不能阻塞！
      - 因此，浏览器选择**异步**来解决这个问题，使用异步的方式，**渲染主线程永不阻塞**
      - 具体做法是当某些任务发生时，比如计时器、网络、事件监听，主线程将任务交给其他线程去处理，自身立即结束任务的执行，转而执行后续代码。当其他线程完成时，将事先传递的回调函数包装成任务，加入到消息队列的末尾排队，等待主线程调度执行。
  2. JS为何会阻碍渲染？
      - 因为js可能会修改dom内容，导致dom需要重新渲染
  3. JS 中的计时器能做到精确计时吗？
      - 计算机硬件没有原子钟，无法做到精确计时
      - 操作系统的计时函数本身就有少量偏差，由于 JS 的计时器最终调用的是操作系统的函数，也就携带了这些偏差
      - 按照 W3C 的标准，浏览器实现计时器时，如果嵌套层级超过 5 层，则会带有 4 毫秒的最少时间，这样在计时时间少于 4 毫秒时又带来了偏差
      - 受事件循环的影响，计时器的回调函数只能在主线程空闲时运行，因此又带来了偏差

-----

## 前端实时通信
### *7种通信方式*
  1. 短轮询
  2. comet
    - 长轮询
    - 长连接
  3. SSE
  4. Websocket
  5. Web Worker
    - 专用线程
    - 共享线程
  6. Service workers
  7. Flash Socket
### *短轮询*
  1. 原理:每隔一段时间客户端就发出一个请求，去获取服务器最新的数据，一定程度上模拟实现了即时通讯。
  2. 优点：兼容性强，实现非常简单
  3. 缺点：延迟性高，请求中有大半是无用，非常消耗带宽和服务器资源，影响性能
  4. 应用： 二维码扫码确认、信息通知
  5. 实现方式：借用定时器实现短轮询
      ```js
        created(){
          this.shortPolling = setInterval(function(){
              that.getRuset()
          },5000)
        },
        methods:{
          getResult(){
            var that = this
            this.$axios('xxx,',post).then(res=>{
              if(res.code === 200){//拿到想要的结果
                ...
                //定时器是否清除由业务场景决定
                clearInterval(this.shortPolling)
              }
            })
          }
        }
      ```
### *comet*
> 服务器推送技术(Server Push),别名叫Comet(彗星）
  1. 长轮询
      - 优点：兼容性好，在无消息的情况下不会频繁的请求，资源浪费较小
      - 缺点：服务器hold连接会消耗资源，返回数据顺序无保证，难于管理维护
      - 应用： webQQ、开心网、校内，Hi网页版、Facebook IM等等
      - 实现方式：
        * 基于 AJAX 的长轮询（long-polling）方式
        * 主要由后端hold住连接，我们就是正常的发送请求即可
        * 务器端会阻塞请求直到有数据传递或超时才返回,客户端会在处理完后，再次发出请求，重新建立连接。
        * 短轮询是在客户端设置定时器，长轮询相当于是在服务端设置定时器
  2. 长连接
      - 优点：兼容性好，消息即时到达，不发无用请求
      - 缺点：服务器维护长连接消耗资源
      - 应用： Gmail聊天
      - 实现方式：
        * 基于 Iframe 及 htmlfile 的流（streaming）方式
        * 在页面里嵌入一个隐蔵iframe，将这个隐蔵iframe的src属性设为对一个长连接的请求或是采用xhr请求，服务器端就能源源不断地往客户端输入数据。 
        ```js
          //在vue中嵌入iframe
          <iframe ref="iframe" v-show="iframeShow"></iframe>

          watchIframe(){
            //先找到iframe的窗口
            this.iframeWin = this.$refs.iframe.contentWindow;
            //向iframe发送信息,大括号内是发送的内容;
            this.iframeWin.postMessage({ },"*");
            //怎样监听iframe传过来的信息
            window.addEventListener("message", this.handleMessage);
            //获取iframe传过来的信息
            handleMessage(res){
              //res为传过来的信息
              ...//渲染页面
            }
          }
        ```
### *SSE*
  1. SSE（Server-Sent Event，服务端推送事件）是一种允许服务端向客户端推送新数据的HTML5技术
  2. 优点：基于HTTP而生，因此不需要太多改造就能使用，使用方便，而websocket非常复杂，必须借助成熟的库或框架
  3. 缺点：基于文本传输效率没有websocket高，不是严格的双向通信，客户端向服务端发送请求无法复用之前的连接，需要重新发出独立的请求，并且不兼容IE
  4. 实现方式：Server-Sent Events也可以自定义事件
    ```js
      var source = new EventSource(url);
      source.addEventListener('open', function (event) {
        // ...
      }, false);
      //客户端收到服务器发来的数据，就会触发message事件，可以在onmessage属性的回调函数。
      source.addEventListener('message', function (event) {
        var data = event.data;
        // handle message
      }, false);

      //如果发生通信错误（比如连接中断），就会触发error事件，可以在onerror属性定义回调函数。
      source.addEventListener('error', function (event) {
        // handle error event
      }, false);

      //close方法用于关闭 SSE 连接。
      source.close();
    ```
### *Websocket*
  1. Websocket是一个全新的、独立的协议，基于TCP协议，与http协议兼容、却不会融入http协议，仅仅作为html5的一部分，其作用就是在服务器和客户端之间建立实时的双向通信。
  2. 优点：真正意义上的实时双向通信，性能好，低延迟
  3. 缺点：独立与http的协议，因此需要额外的项目改造，使用复杂度高，必须引入成熟的库，无法兼容低版本浏览器
  4. 实现方式：Server-Sent Events也可以自定义事件
    ```js
      var ws = new WebSocket("wss://echo.websocket.org");
      // 当连接建立成功，触发 open 事件
      ws.onopen = function(evt) {
        console.log("建立连接成功 ...");
        // 连接建立成功以后，就可以使用这个连接对象通信了
        // send 方法发送数据
        ws.send("Hello WebSockets!");
      };

      // 当接收到对方发送的消息的时候，触发 message 事件
      // 我们可以通过回调函数的 evt.data 获取对方发送的数据内容
      ws.onmessage = function(evt) {
        console.log("接收到消息: " + evt.data);
        // 当不需要通信的时候，可以手动的关闭连接
        // ws.close();
      };

      // 当连接断开的时候触发 close 事件
      ws.onclose = function(evt) {
        console.log("连接已关闭.");
      }
    ```
  5. WebSocket心跳机制
    - 原理：利用心跳包及时发送和接收数据，保证WebSocket长连接不被断开。（即：客户端和服务器之间定时发送空数据包检测连接状态或停止无通信时的长连接）
    - 心跳包：WebSocket协议的保活机制，用于维持长连接。心跳包是指在一定时间间隔内，WebSocket发送的空数据包
    - 作用：
      * 保持WebSocket连接不被断开。
      * 检测WebSocket连接状态，及时处理异常情况。
      * 减少WebSocket连接及服务器资源的消耗。
    - 前端实现：
      * 使用setInterval定时发送心跳包。
      * 在前端监听到WebSocket的onclose()事件时，重新创建WebSocket连接。
### *Web Worker*
  1. Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行
  2. 优点：实现多线程环境，摆脱了js的单线程
  3. 缺点：无法访问DOM节点；无法访问全局变量或是全局函数；无法调用alert()或者confirm之类的函数；无法访问window、document之类的浏览器全局变量；
  4. 注意事项：Web Worker中的Javascript依然可以使用setTimeout(),setInterval()之类的函数，也可以使用XMLHttpRequest对象来做Ajax通信
  5. 应用：大数据的处理；高频的用户交互
  6. 实现方式：分为两种类型：专用线程、共享线程。
  7. 专用线程
    - 随当前页面的关闭而结束；
    - 只能被创建它的页面访问。
    - 使用 onmessage() ， postmessage()通信
    ```js
      /** 主线程 **/
      //注册专用线程 参数是要在专用线程里执行的js文件
      let worker = new Worker('worker.js')
      worker.onmessage = (e) => {
        console.log(e.data) // I post a message to main thread
      }
      worker.postMessage('main thread got a message')
      // 在主线程中终止
      worker.terminate()
      
      /**  子线程 worker.js  **/
      onmessage = (e) => {
          console.log(e.data) // main thread got a message
      }
      postMessage('I post a message to main thread')
      
      // 在子线程中终止自身
      self.close()
    ```
  8. 共享线程
    - 可以被多个页面访问
    - 需要用到port属性，接收需要先connect
    ```js
      //注册共享线程
      let worker = new SharedWorker("sharedworker.js");
      /**  主线程  **/
      worker.port.onmessage = function(e){}
      worker.port.postMessage('data');
      // 在主线程中终止
      worker.terminate()
      
      /** 子线程 **/
      addEventListener('connect', function(event){
          var port = event.ports[0]
          //接收
          port.onmessage = function(event){
              console.log(event.data);
          };
          //发送
          port.postMessage("data");
          port.start();
      });
      // 在子线程中终止自身
      self.close()
    ```
  9. 错误监听：两种方式的错误监听同SSE一样
    ```js
      worker.addEventListener("error", function(evt){  
        alert("Line #" + evt.lineno + " - " + evt.message + " in " + evt.filename);  
      }, false);  
    ```
### *Service workers*
  1. Service workers 本质上充当Web应用程序与浏览器之间的代理服务器，也可以在网络可用时作为浏览器和网络间的代理，创建有效的离线体验。 它是 Web Worker 的一个类型
  2. 优点：可以秒开或者离线访问
  3. 缺点：IE11 、Opera Mini 、IOS不支持
  4. 应用：推送通知 — 允许用户选择从网络应用程序及时更新。
  4. 实现方式：
    - vue项目中引入`register-service-worker`npm包，添加js配置文件
    ```js
      // serviceWorker.js
      import { register } from 'register-service-worker'

      if (process.env.NODE_ENV === 'production') {
        register('service-worker.js', {
          ready () {
            console.log(
              'App is being served from cache by a service worker.'
            )
          },
          registered () {
            console.log('Service worker has been registered.')
          },
          cached () {
            console.log('Content has been cached for offline use.')
          },
          updatefound () {
            console.log('New content is downloading.')
          },
          updated () {
            console.log('New content is available; please refresh.')
            window.location.reload(true)   // 这里需要刷新页面
          },
          offline () {
            console.log('No internet connection found. App is running in offline mode.')
          },
          error (error) {
            console.error('Error during service worker registration:', error)
          }
        })
      }
    ```
    - 在 plugins 加入
    ```js
      plugins: [
        new SWPrecacheWebpackPlugin({
          cacheId: 'my-project-name',
          filename: 'service-worker.js',
          staticFileGlobs: ['dist/**/*.{js,html,css}'],
          minify: true,
          stripPrefix: 'dist/'
        }),

        new WebpackPwaManifest({
          name: 'My Progressive Web App',
          short_name: 'MyPWA',
          description: 'My awesome Progressive Web App!',
          background_color: '#ffffff',
          crossorigin: 'use-credentials', //can be null, use-credentials or anonymous
          icons: [
            {
              src: path.resolve('src/assets/icon.png'),
              sizes: [96, 128, 192, 256, 384, 512] // multiple sizes
            },
            {
              src: path.resolve('src/assets/large-icon.png'),
              size: '1024x1024' // you can also use the specifications pattern
            }
          ]
        }),
      ]
    ```
    - 这样打包出来的代码根目录里面多了个 service-worker.js ，html文件里面 pwa 相关元素也加上了。
    - 在入口 main.js 引入该文件：
    ```js
      import './serviceWorker'
    ```
### *Flash Socket*
  1. 优点：实现真正的即时通信，而不是伪即时。
  2. 缺点：客户端必须安装Flash插件；非HTTP协议，无法自动穿越防火墙。
  3. 实例：网络互动游戏。
  4. 实现方式：
    - 在页面中内嵌入一个使用了Socket类的 Flash 程序
    - JavaScript通过调用此Flash程序提供的Socket接口与服务器端的Socket接口进行通信
    - JavaScript在收到服务器端传送的信息后控制页面的显示。
    - 一般用在网络游戏中，web端基本不适用
### *短轮询和长轮询对比*
  1. 长轮询与服务器的通信会比短轮询更实时
  2. 短轮询是在采用定时器，定时器就有时间差，比如我们定时5秒钟。可能一秒钟内我们就实现了数据通信，那么会有4秒的等待时间。而长轮询就可以避免这个顾虑。
  3. 可以使用回调递归实现实时通信，类比短轮询，但是回调递归并发量太大了，很容易造成服务器死机，并且消耗宽带影响到前端性能，所以我们正常不会使用回调递归实现，而且回调递归也不叫作短轮询