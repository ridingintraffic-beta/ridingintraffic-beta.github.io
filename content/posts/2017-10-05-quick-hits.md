---
layout: single
title: '2017-10-05 quick wins'

---

Today is going to be a quick one wiht some "scripts" and or functions that were given to me and a couple that I wrote on my own.  simply create the bash script add them to a folder and then add that path to your $PATH variable in your bash_profile.

# ssh-trust
This one was given to me from a co-worker and solves the issue of validating a changed ssh-host key when that host changes.  Instead of going in and deleting the entry manually from your authorized_hosts file...

```
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

if [[ $# -ne 1 ]]; then
  echo 'usage: ssh-trust <line-number>' >&2
  exit 1
fi

gsed -i "${1}d" ~/.ssh/known_hosts
 ```

# docker_logs_latest
often times I will need to jsut tail the logs of the last docker container that I spun up,  this will grab the most recent;y launched docker container and then `docker logs -f` that container id
```
#!/bin/bash

$(docker logs -f “$(docker ps -l -q)“)
```

# dockerconn
I am pretty bad at remembering what the attach process is for dropping in to a docker container, so this just need s docker_id and I can attach to the container. 
```
#!/bin/bash

set -euo pipefail
IFS=$'\n\t'

if [[ $# -ne 1 ]]; then
  echo 'usage: dockerconn <container_id>' >&2
  exit 1
fi

docker exec -i -t ${1} /bin/bash
```

There you have it a couple quick and dirty scripts to make your life easier.  I hope that you enjoyed!