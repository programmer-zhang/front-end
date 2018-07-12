### 浏览器同域并发请求数量问题
* 基于端口数量和线程切换开销的考虑,浏览器不可能无限量的并发请求
* 并非越大越好，基于良知和默契的考虑，保护浏览器和服务器更好的性能
* 迅雷、暴风影音等可以修改电脑的最大连接数
* 用户可以重写浏览器的默认值(IE)
* 目前浏览器的最大同域并发请求数量
	
Browser|Max
:--:|:--:
IE8,9| 6
Firefox|6
Chrome|6

* [已有解决方案](https://www.zhihu.com/question/20474326/answer/15696641)：domain hash, cookie free, css sprites, js/css combine, max expires time, loading images on demand