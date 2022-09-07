---
layout: single
title: 2019-05-01 curl-redirects
modified:
categories: blog
date: 2019-05-01-T08:08:50-04:00
---

# curl redirects
Sometimes you want to be able to follow a redirect chain to see what is going on.<br>
you can do that pretty easily with curl. <br>
Toss in your url and hit enter then follow the breadcrumbs.
```
curl -v -L http://google.com 2>&1 | egrep "^> (Host:|GET)"
```

``` 
$ curl -v -L http://google.com 2>&1 | egrep "^> (Host:|GET)"
> GET http://google.com/ HTTP/1.1
> Host: google.com
> GET http://www.google.com/ HTTP/1.1
> Host: www.google.com

```
