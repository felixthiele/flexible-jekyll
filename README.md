# Personal Blog Page
Based on [Flexible Jekyll](https://github.com/artemsheludko/flexible-jekyll)

**Run locally using**

```sh
rm -rf _site/ && docker run --rm --volume="$PWD:/srv/jekyll" -it jekyll/jekyll jekyll build && http-server _site/
```