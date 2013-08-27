---
layout: post
title: 迁移WordPress博客至Octopress
date: 2013-03-19 22:32
comments: true
categories: octopress
---

####安装GIT####

{% codeblock lang:java %}
yum -y install git
{% endcodeblock %}

####安装RVM需要的包####

{% codeblock lang:java %}
sudo yum install bash curl patch
{% endcodeblock %}

####安装RVM####

{% codeblock lang:java %}
curl -L https://get.rvm.io | bash -s stable --ruby
{% endcodeblock %}

####安装Ruby需要的包####

{% codeblock lang:java %}
sudo yum install gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison iconv-devel java
{% endcodeblock %}

####安装Ruby 1.9.3####
{% codeblock lang:java %}
rvm install 1.9.3
rvm use 1.9.3
rvm rubygems latest
{% endcodeblock %}

####下载octopress####

{% codeblock lang:java %}
git clone git://github.com/imathis/octopress.git dengdezhao
cd dengdezhao
ruby --version	*#应该显示1.9.3*
gem install bundler
bundle install
rake install
{% endcodeblock %}

####出现Warning:Insecure world writable dir####
这个一般是由于权限的问题，我出现这个问题是因为是用ROOT安装的RVM，然后用其它用户访问，为了方便我直接把/usr/local目录权限设置为777，然后就弹出这个警告了，/usr/local目录很重要，不允许非owner访问，所以设置为755即可解决该问题。
{% codeblock lang:java %}
sudo chmod -R 755 /usr/local
{% endcodeblock %}

####将Wordpress数据导出为XML文件####

进入Wordpress后台，将全站数据保存为XML文件，放在Octopress根目录下

####下载迁移工具####
https://gist.github.com/chitsaou/1394128    
下载**wordpressdotcom.rb**放到Utils目录中，执行命令
{% codeblock lang:java %}
ruby -r "./utils/wordpressdotcom.rb" -e "Jekyll::WordpressDotCom.process"
{% endcodeblock %}

运行出错，需要安装hpricot

{% codeblock lang:java %}
gem install hpricot
{% endcodeblock %}
安装成功之后再执行上面的迁移命令，即可把wordpress中的文章导入到octopress的source/_posts中了

####修改文章图片链接####

迁移过来的文章中图片链接可能不大对，需要修改才能让图片显示正常，把原来wp-contents目录下uploads文件夹拷到相应路径下，替换掉文件中的静态地址为图片地址即可避免点击图片时出现404错误。  

####修改页面名字及新增[标签云]&[关于]页面####

修改navigation.html
{% codeblock lang:java %}
vi source/_includes/custom/navigation.html 
{% endcodeblock %}
给各页面修改个性化名字，并新增两个页面
{% codeblock lang:ruby %}
<ul class="main-navigation">
  <li><a href="{{ root_url }}/">博客主页</a></li>
  <li><a href="{{ root_url }}/blog/archives">文章列表</a></li>
  <li><a href="{{ root_url }}/tag">标签云</a></li>
  <li><a href="{{ root_url }}/about">关于</a></li>
</ul>
{% endcodeblock %}

创建tag&about页
{% codeblock lang:ruby %}
rake new_page[tag]
rake new_page[about]
{% endcodeblock %}
编辑页面内容，并下载配置标签云插件
{% codeblock lang:ruby %}
git clone https://github.com/robbyedwards/octopress-tag-pages.git
git clone https://github.com/robbyedwards/octopress-tag-cloud.git
{% endcodeblock %}
将下载的文件拷贝至相应目录，并在_config.yml中增加一行	`tag_dir: tags`

####生成静态页面及预览####
以上操作完成之后，执行`rake generate`命令生成静态页面，然后`rake preview`，在浏览器中输入http://localhost:4000即可预览站点。



