---
layout: post
title: "install_apple_gcc42"
date: 2012-12-10 14:14
comments: true
categories: homebrew gcc
---

###
Install apple-gcc42 by homebrew,
###

####
```bash
brew tap homebrew/dupes
brew install apple-gcc42
```

if it is too slow, you can download it manually.

1. looking for Formula by http://braumeister.org/, or 

  find the url in 'https://github.com/Homebrew/homebrew-dupes/blob/master/apple-gcc42.rb',
  it is 'http://r.research.att.com/tools/gcc-42-5666.3-darwin11.pkg',
  version is '4.2.1-5666.3', maybe not the lastest as time pass by.

2. puts this in /usr/local/Library/Caches/Homebrew/, run again.

ps:
  Sometimes it will appear errors like: 'make: /usr/bin/gcc-4.2: No such file or directory'.
  The resolution is link apple-gcc42 to gcc-4.2

```bash
  sudo ln -s /usr/local/bin/gcc-4.2 /usr/bin/gcc-4.2
```

pps:
if you run 'brew install appe-gcc42', it says counldn't found any Formula named apple-gcc42

first, try brew tap homebrew/dupes, then run again.

seaond, download the 'https://github.com/Homebrew/homebrew-dupes/blob/master/apple-gcc42.rb', put it into
/usr/local/Library/Formula/, then try again
####

