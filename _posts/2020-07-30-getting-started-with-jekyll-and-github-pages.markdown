---
layout: post
title: Getting Started with Jekyll & GitHub Pages
date: 2020-07-30 12:00:00 +00
description: How to get started with a personal blog in Jekyll and hosting it with GitHub Pages
img: posts/2020-07-30/title.jpg
tags: [Tools]
---

I recently looked into static site generators and the possibilities they offer based on their simplicity and performance for a multitude of tasks. One of those tasks is writing your own blog. Compared to setting up Wordpress or comparable software, the whole process is much more lean and simple. And the result feels great for developers, as the whole writing experience is very close to coding! In this post I will show you how to set up your own blog using the static site generator Jekyll and publishing it to GitHub Pages - all for free.

## Setting up Jekyll
### Finding a Jekyll template
The first step to your own blog is to find a template which you can customize to your liking. I got mine from [jekyllthemes.io](https://jekyllthemes.io/), which I found quite well organized between purposes (blog-themes, sites) and free and paid themes. I personally picked a free, blog-purposed theme ([Flexible Jekyll](https://jekyllthemes.io/theme/flexible-jekyll)) for which the source code was published on GitHub. From there I simply [forked](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) the repository to my own account.

### Overall structure
After forking you are presented with a folder structure similar to the following:

| File- / Foldername    | Purpose                   |
| --------------------- | ------------------------- |
| _includes             | HTML snippets that can be included in all other HTML files in the project using the `{% raw %}{% include X %}{% endraw %}` syntax, where X is the name of the file |
| _layouts              | Each post can have a specific layout which itself is defined in this directory. Layouts can extend other layouts as well. |
| _posts                | The posts of your blog. All filenames must follow the format `yyyy-mm-dd-title.markup`. |
| _sass                 | The styling of the site. |
| _site                 | Only appears after you build the site and contains the transformed files. Put this directory on `.gitignore` |
| _config.yml           | The main configuration file of the site. Keys in this file can be accessed from templates with `site` |
| assets                | All static assets like images, fonts. Have one scss file here including the ones from `_sass`. |
| index.html            | The main HTML file. |

All folders and files not starting with an underscore will be part of the finished build. Make sure to exclude other files like `Gemfile` or `README.MD` by defining an exclude section in the `_config.yml`:

```yml
exclude:
  - Gemfile
  - README.MD
```

Jekyll will process all files that start with a so-called [Front Matter](https://jekyllrb.com/docs/front-matter/). With the Front Matter you can pass variables to Jekyll. The following global variables exist:

| Name                  | Purpose                   |
| --------------------- | ------------------------- |
| layout                | Defines which layout should be applied to the page. |
| permalink             | For creating a specific link for the page that differs from the automatically generated |
| published             | Set to false to hide the page in the transformed site. |


### Customizing & First Blog Post
To customize the site edit the `_config.yml` and set the variables that the template author defined. Additionally, check the styling of the page and make it your own. Finally delete the example blog posts from the `_posts`-directory and create your own first post. The name of the file must comply with the naming scheme `yyyy-mm-dd-title.markdown`, so for example `2020-06-08-getting-started-with-jekyll.markdown`. All blog posts do need to start with a valid Front Matter:

```
---
layout: post
title:  "Welcome to Jekyll!"
---

# Welcome

**Hello world**, this is my first Jekyll blog post.
```

In the Front Matter fill all the variables predefined by the author of your template.

### Local preview
Setting up jekyll locally seemed to me quite cumbersome, as it is based on Ruby and thus requires a Ruby installation. To circumvent this, there exists a [docker image](https://hub.docker.com/r/jekyll/jekyll/). The docker image brings all necessary installations and configurations with it and allows us to execute commands easily.

```sh
docker run --rm --volume="$PWD:/srv/jekyll" -p 4000:4000 -it jekyll/jekyll \
  jekyll serve --watch
```

Lets break that statement down:

* `--rm` makes sure that the docker container and volumes are disposed after using, so that your local drive is not cluttered up
* `--volume="$PWD:/srv/jekyll"` mounts the current folder (`$PWD`) into the docker container at the right place where the container expects jekyll content
* `-p 4000:4000` maps port 4000 inside the container to your local port.
* `-it jekyll/jekyll jekyll serve --watch` spawns an interactive shell in the `jekyll/jekyll` container and runs `jekyll serve --watch`. This will cause jekyll to build your project, start a server and continuously watch for file changes to retrigger a build.

With this you can locally build your site without hassle. The result of the build is stored in the `_site` directory.

### Styling source code
In case your blog features source code snippets, you might want to fiddle around with the syntax hightlighting. Jekyll under the hood uses [Rouge](http://rouge.jneen.net/) to highlight source code. Rouge is 100% compatible with [Pygments](https://pygments.org/) themes which you can find easily on the internet. I recommend checking out [StylishThemes](https://stylishthemes.github.io/Syntax-Themes/pygments/) or [Pygments CSS Themes](http://jwarby.github.io/jekyll-pygments-themes/languages/java.html), selecting a css file and dropping it as an `scss`-file into your `_sass`-folder.

## Setting up GitHub to host your site
Setting up GitHub to expose your site is a piece of cake: simply rename your repository to `yourgithubusername.github.io` and GitHub will take care of building your site and hosting it. Soon after you should see your shiny new site online under https://yourgithubusername.github.io.

I personally took it one step further and pointed my domain to my GitHub Page. This [help-article](https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site) explains how to do that.

## Conclusion
In this blog post we have learned how to set up a simple blog using Jekyll and GitHub Pages, that is blazing fast, lightweight and maintainable. 