---
title: Setup and run DEVHINTS.IO cheatsheets on localhost
date: 2018-06-08 17:32:39
tags:
---

This is a static website based on Jekyll framework https://jekyllrb.com/. Need install Jekyll and .

Login as user "root" due to write permission required into **/Library/Ruby/Gems** directory. Install **bundler** and **Jekyll** at first:

```bash
igloo:cheatsheets root# gem install bundler jekyll
```

Then install all this application dependent libraries:

```bash
igloo:cheatsheets root# bundler install
```

Start up Jekyll website on localhost, with user "terrence" this time:

```bash
terrence@igloo ~/Projects/cheatsheets (master ☡=)
17:28:56 𝜆 bundle exec jekyll serve --incremental
Configuration file: /Users/terrence/Projects/cheatsheets/_config.yml
            Source: /Users/terrence/Projects/cheatsheets
       Destination: /Users/terrence/Projects/cheatsheets/_site
 Incremental build: enabled
      Generating...
DEPRECATION WARNING on line 83, column 12 of /Users/terrence/Projects/cheatsheets/_sass/vendor/modularscale/_modularscale.scss:
Unescaped multiline strings are deprecated and will be removed in a future version of Sass.
To include a newline in a string, use "\a" or "\a " as in CSS.

                    done in 3.163 seconds.
 Auto-regeneration: enabled for '/Users/terrence/Projects/cheatsheets'
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
```

Go to URL: http://127.0.0.1:4000 start searching and using cheatsheets in localhost environment:

![DEVHINTS.IO - cheatsheets](https://ph-files.imgix.net/9585bd9a-5b33-4c1d-bda2-f285fbd5f9ae "DEVHINTS.IO - cheatsheets")

![DEVHINTS.IO - cheatsheets](https://ph-files.imgix.net/a04245b3-65d1-4e27-b6ce-45216f479063 "DEVHINTS.IO - cheatsheets")

To generate static web content ONLY:

```bash
terrence@igloo ~/Projects/cheatsheets (master ☡=)
17:28:56 𝜆 bundle exec jekyll build
Configuration file: /Users/terrence/Projects/cheatsheets/_config.yml
            Source: /Users/terrence/Projects/cheatsheets
       Destination: ../terrencemiao.github.io/cheatsheets
 Incremental build: disabled. Enable with --incremental
      Generating...
DEPRECATION WARNING on line 83, column 12 of /Users/terrence/Projects/cheatsheets/_sass/vendor/modularscale/_modularscale.scss:
Unescaped multiline strings are deprecated and will be removed in a future version of Sass.
To include a newline in a string, use "\a" or "\a " as in CSS.

                    done in 43.269 seconds.
 Auto-regeneration: disabled. Use --watch to enable.
```

Then can upload published website content from ../terrencemiao.github.io/cheatsheets directory to https://terrencemiao.github.io.