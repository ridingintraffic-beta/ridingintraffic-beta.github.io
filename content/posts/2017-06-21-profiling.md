---
layout: single
title: '2017-06-21, profiling'

---

# Node profiling
There is a handy dandy performance profileer that I made use of today. 
nodejs-dashboard  [nodejs-dashboard](https://github.com/FormidableLabs/nodejs-dashboard "nodejs-dashboard")
It is easily configured to be added at runtime with: `nodejs-dashboard -- node -r nodejs-dashboard index.js`
if you check the git repo you can see all the magic that can be done with the dashboards.   I find it useful for heap usage and cpu.


# docker gotcha
There is a feature in docker called autorestart. Autorestart is a service-level setting that can automatically start your containers if they stop or crash.  Depending how you are doing the storage mapper for docker, if you are using devicemapper and mounting that.   Then when you restarts are tirggered it will use all of the device mapper it used before, at this point it maps more space.  Depending how muc your space your container is using it can quickly chew up all of the device mapper storage space on your host. 