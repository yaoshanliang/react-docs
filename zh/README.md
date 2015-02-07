# React文档和网站

我们使用[Jekyll](http://jekyllrb.com/)来构建这个网站，该网站（[绝大部分](http://zpao.com/posts/adding-line-highlights-to-markdown-code-fences/)）使用Markdown，我们通过提交HTML文件到[GitHub Pages](http://pages.github.com/)来发布该网站。

## 安装

如果你要在此网站上做修改，你将会想安装运行一个本地副本。

### 依赖

为了使用Jekyll，你需要安装Ruby。

 - [Ruby](http://www.ruby-lang.org/) (version >= 1.8.7)
 - [RubyGems](http://rubygems.org/) (version >= 1.3.7)
 - [Bundler](http://gembundler.com/)

Mac OS X预装了Ruby，但是你可能需要更新RubyGems（通过`gem update --system`命令）。
否则，[RVM](https://rvm.io/)和[rbenv](https://github.com/sstephenson/rbenv)是安装Ruby的流行方式。
一旦你拥有了RubyGems，并且安装了Bundler(通过`gem install bundler`命令)，使用它安装如下依赖：

```sh
$ cd react/docs
$ bundle install # 可能需要sudo。
$ npm install # 可能需要sudo。
```

### 指令

这个网站需要React，所以第一步确保你已经构建了这个项目（通过`grunt`）。

使用Jekyll来启动本地网站（默认地址是`http://localhost:4000`）：

```sh
$ cd react/docs
$ bundle exec rake
$ bundle exec jekyll serve -w
$ open http://localhost:4000/react/
```

我们使用[SASS](http://sass-lang.com/)（附带[Bourbon](http://bourbon.io/)）来生成CSS，使用JSX转换成我们的部分JS。
如果你仅仅想修改HTML或者Markdown，你不需要做任何事，因为我们打包了预编译的CSS和JS副本。
如果你想修改CSS或者JS，使用[Rake](http://rake.rubyforge.org/)来编译它们：

```sh
$ cd react/docs
$ bundle exec rake watch # Automatically compiles as needed.
# bundle exec rake         Manually compile CSS and JS.
# bundle exec rake js      Manually compile JS, only.
```

## 接下来（Afterthoughts）

### 更新 `facebook.github.io/react`

做这件事最简单的方式是拥有一个独立的此仓库副本，签出`gh-pages`分支。我们有一个构建步骤，这些步骤需要该分支在`react-gh-pages`目录下，并且此目录和`react`在同一目录。然后就仅仅是一个运行`grunt docs`命令的问题了，这将会编译该网站，将网站拷贝出当前仓库。在那里，你可以提交编译后的内容到服务器。

**注意：** 应该仅仅在有新发布的时候才做这件事。你应该创建一个标签，对应于发布主仓库的发布标签。

我们也有rake任务来做相同的事情（不创建提交）。该任务需要上面提到的目录结构。

```sh
$ bundle exec rake release
```

### 移除Jekyll / Ruby依赖

在理想的情况下，我们不会添加一个Ruby依赖作为我们项目的一部分。我们愿意使用React来渲染网站。
