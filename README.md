# sphenginx.github.io

__Deploy this blog with hexo!__
 - First, install [node.js v5.1 stable](https://nodejs.org/en/)
 - install [hexo io](https://hexo.io/)

```
$ npm install hexo-cli -g 
$ npm install hexo --save 
```
 - init hexo folder

安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```
$ hexo init <folder> 
$ cd <folder> 
$ npm install 
```
 - install hexo plugins

```plugins
$ npm install hexo-generator-index --save
$ npm install hexo-generator-archive --save
$ npm install hexo-generator-category --save
$ npm install hexo-generator-tag --save
$ npm install hexo-generator-feed@1 --save
$ npm install hexo-generator-sitemap@1 --save
$ npm install hexo-server --save
$ npm install hexo-deployer-git --save
$ npm install hexo-deployer-heroku --save
$ npm install hexo-deployer-rsync --save
$ npm install hexo-deployer-openshift --save
$ npm install hexo-renderer-marked@0.2 --save
$ npm install hexo-renderer-stylus@0.2 --save
```
 - use && debug

部署到Github前需要配置_config.yml文件。
```
deploy:
  type: github 
  repository: https://github.com/sphenginx/sphenginx.github.io.git 
  branch: master 
```

之后执行下列指令即可完成部署，注意部署会覆盖掉你之前在版本库中存放的文件。

```
$ hexo clean 
$ hexo generate 
$ hexo deploy 
```

如果需要预览或者调试你的博客，可以启动本地服务器：

```server
$ hexo server 
```
然后在浏览器输入 `http://localhost:4000` 即可查看

>注：修改配置文件时注意`YAML`语法，参数`冒号:`后一定要留空格

__QA__
- Question：Win platform first commit code appear `git replacing LF with CRLF` notice？
- Answer：[With stackoverflow helper](http://stackoverflow.com/questions/1967370/git-replacing-lf-with-crlf), set config autocrlf info below:   
```
$ git config core.autocrlf false
```

__Helper url__
 - [使用GitHub和Hexo搭建免费静态Blog](http://wsgzao.github.io/post/hexo-guide/)
 - [使用hexo搭建博客](http://yangjian.me/workspace/building-blog-with-hexo/) 
 - [Hexo在github上构建免费的Web应用](http://blog.fens.me/hexo-blog-github/)
 - [hexo-theme-next](https://github.com/iissnan/hexo-theme-next)