---
title: Hexo 同时使用两种主题
urlname: Use-Two-Themes-in-Hexo
tags: [hexo]
date: 2019-03-16 22:30:26
updated: 2019-03-16 22:30:26
---

## Context

在Hexo主页面的catagory下增加了wiki项，但是想利用一个不同的theme生成另一个网页，放置在子目录下。因此尝试能否在一个仓库内分别渲染生成两个不同的theme。（只是想测试下可行性，其实完全可以建两个hexo仓库分别渲染生成页面，再将wiki页面复制到了主页面的public/wiki/目录下）。

### 想法

root目录下有两个config文件，默认使用_config.yml，用`hexo --config config_wiki.yml g`命令渲染wiki页面至`/public/wiki/`，同时设置点击主页面`wiki`的category后会自动链接到`public/wiki/`。



## 修改步骤

1. 更改原theme（主页面）的config文件，将新增category类别（不同主题设置有区别）。

   ```diff
   nav:
   	Wiki: /wiki/
   ```

2. 为了在修改wiki配置时避免影响到主页面，将root目录下的`_config.yml`文件复制并重命名为`_config_wiki.yml`，以下修改均指此文件。

3. 安装hexo的[Wikitten](https://github.com/zthxxx/hexo-theme-Wikitten)主题，将其作为wiki页面的theme

   ```shell
   cd your-hexo-directory
   git clone https://github.com/zthxxx/hexo-theme-Wikitten.git themes/Wikitten
   ```

4. 将Wikitten theme目录下clone所得的source和模板移动到root目录下，重命名主题config使其生效。

   ```shell
   cp -rf themes/Wikitten/_source/* source_wiki/
   cp themes/Wikitten/_scaffolds/embed.md scaffolds/embed.md
   cp themes/Wikitten/_scaffolds/post.md scaffolds/wiki.md
   cp -f themes/Wikitten/_config.yml.example themes/Wikitten/_config.yml
   ```

5. 修改wikitten主题的config，更改资源文件目录，icon、favicon等。

6. 修改`_config_wiki.yml`设定使wikitten主题生效

   ```diff
   - theme: origin
   + theme: Wikitten
   
   - per_page: 10
   
   - url: https://konfido.github.io/
   - root: /
   - permalink: :year/:month/:day/:title/
   + url: https://konfido.github.io/wiki/
   + root: /wiki/
   + permalink: :urlname/
   + permalink_defaults: :year/:month/:day/:title/
   
   + skip_render:
   +  - README.md
   
   + marked:
   +   gfm: true
   
   + # 设置单独的source文件夹，用于放置wiki的源文件
   - source_dir: source
   + source_dir: source_wiki
   
   + # 设置渲染到`public/wiki/`路径下
   - public_dir: public
   + public_dir: public/wiki
   
   + default_layout: wiki
   ```


## 使用

- 视自己情况在`.zshrc`或者`.bashrc`等文件中新增alias，以简化命令的输入。

  ```shell
  alias hw="hexo --config _config_wiki.yml"
  ```

- 新建wiki

  ```shell
  hw new "xxxx"
  ```

- 渲染步骤

  共用db.json（猜測）会导致主页面blog内容与wiki内容混杂，需要在渲染前后进行clean操作。

  ```shell
  # 首先渲染主页面，生成到/public/
  hexo g
  # 删除db.json及旧的public/wiki
  hw clean
  # 渲染wiki页面，生成到/public/wiki/
  hw g
  # 手动删除db.json，MUST！
  rm db.json
  # 查看效果
  hexo s
  ```