## Website Performance Optimization portfolio project

### Getting started

#### Part 1: Optimize PageSpeed Insights score for index.html

1. 配置你的Web Server, 如果用的Mac, 本身就带有Python, 如果其他系统，请先安装Python，这里我们用的是Python自带的Web Server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

2. 打开浏览器，最好用Chrome浏览器，访问localhost:8080
3. 下载和安装 [ngrok](https://ngrok.com/) 到你项目的根目录，这个工具帮助你建立一个可以在公网上访问的域名。

  ``` bash
  $> cd /path/to/your-project-folder
  $> ./ngrok http 8080
  ```

4.拷贝ngrok提供的公网域名，打开谷歌的[PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)，然后就可以测试你网站的性能，但是如果访问谷歌站点有问题，需要配置你的VPN。如果需要更多信息，请访问[如何结合grunt/pagespeed/ngrok的文章](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

5. 压缩图片，可以使用[tinypng](https://tinypng.com)或者另外一个工具[Reduced Image](http://www.reduceimages.com),把img和views/images两个目录下的图片都给检查一下，能压的都压一下，尤其是pizzeria.jpg,原件300多K，确实影响加载时间，而我们的html源文件对此图片的要求是```img style="width: 100px;```，压缩后其实只有不到5K。
6. 就是内连CSS文件和小的js文件，把它们压缩后，如果很小可以直接内连在html文件
7. css的media属性，如果确实和rendor无关，可以加这个属性，比如media="print"。
`` html
  <link href="css/print.min.css" rel="stylesheet" media="print">
``
8. JS的异步处理，对谷歌的分析程序和rendor无关，因此可以异步或者defer，我这里用的是异步处理。
`` js
  <script async src="http://www.google-analytics.com/analytics.js"></script>
``
9.对谷歌的字体css样式，变成JS来调用，这个很好处理加速了性能
`` js
  <script src="https://ajax.googleapis.com/ajax/libs/webfont/1.5.18/webfont.js"></script>
  <script>
     WebFont.load({
        google: {
          families: ['Open Sans:400,700']
        }
      });
  </script>
``
 
10. 其他资源. 
+ Compress JPG/PNG to small size by using [tinypng](https://tinypng.com).
+ Configure your grunt env by reading [GRUNT tool](http://www.gruntjs.net), follow the sample to create your Gruntjs.file and package.json files.

#### Part 2: Optimize Frames per Second in pizza.html

1. 这个项目最主要的是要学会利用Chrome的Dev tools来进行动画优化，如何使用这个工具，可以参考文章[Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

2. 学习体会：
1） CSS Style将会影响layout/painting/composite, 我们在循环里面要尽量减少layout／painting，composite的开销不大，可以把所有需要relayout的语句都转化为composite。例如， css style.transform is a good way to improve performance, because it is only have impact on composite. You can learn more from [CSS Triggers website](https://csstriggers.com).
2）减少移动pizzas数目仅仅满足屏幕需要即可.比如changePizzaSizes(size)函数，和之前比把DOM的操作移出来，不要loop每次都去DOM操作，真正循环的语句只有一个，大大减少了浏览器的开销。
`` js
  function changePizzaSizes(size) {
      var randomPizzas = document.getElementsByClassName("randomPizzaContainer");
      var dx = determineDx(randomPizzas[0], size);
      var newwidth = (randomPizzas[0].offsetWidth + dx) + 'px';
      for (var i = 0; i < randomPizzas.length; i++) {
        randomPizzas[i].style.width = newwidth;
      }
  }
``
3) 在两个大循环里面（changeSize和updatePosition），把一些DOM操作语句都要移出来，否则开销很大。
4) 使用requestAnimationFrame API来提高动画性能, learn more by going [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame) and [Creative JS](http://creativejs.com/resources/requestanimationframe/index.html)。它实际上是一个回归调用。
`` js
  updatePositions(){
    //Todo, your code here
    ...
    reqeustAnimationFrame(updatePositions);
  }
``
5. CSS querySelector的性能比getElementById要差，尽可能替换。
6. 下面列举了很多性能优化提示和信息，非常有帮助，尤其要借助Chrome的Dev Tools一点点来优化，祝成功.

### Optimization Tips and Tricks
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* [Measuring with Navigation Timing](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp.html "nav timing api"). We didn't cover the Navigation Timing API in the first two lessons but it's an incredibly useful tool for automated page profiling. I highly recommend reading.
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/eliminate-downloads.html">The fewer the downloads, the better</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer.html">Reduce the size of text</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching.html">HTTP caching</a>

### Customization with Bootstrap
The portfolio was built on Twitter's <a href="http://getbootstrap.com/">Bootstrap</a> framework. All custom styles are in `dist/css/portfolio.css` in the portfolio repo.

* <a href="http://getbootstrap.com/css/">Bootstrap's CSS Classes</a>
* <a href="http://getbootstrap.com/components/">Bootstrap's Components</a>
