---
date: 2022-08-07T00:00:00-05:00
title: "replacing text across files"
draft: false
categories: [ daily ]
tags : [ devops, automation, scripting ]
---

We built microservices to leverage scale.
As a result those microservices are each in their own repos.
We don't need to get into a discussion about mono repos here, we aren't using them.
The example here is a gitlab pipeline file and we needed to add a new yaml anchor block to use later in the pipeline.
We are going to look for something that we know exists, in this case: ".plan"
Then we are going to add a block of stuff before that.  Its useful to think backwards here because most of the python tools out there search files line by line and then add the replacement before that line.
A standard library that does this well is fileinput.
This pattern allows you to pass in file by file and replace the text in each of those files.
It is super helpful when you have changes spread across tons and tons of files

[fileinput doc](https://docs.python.org/3/library/fileinput.html "fileinput doc")

```
#!/usr/bin/env python3
"""
this is a python script to look for a single line in a file
then replace that line with many lines
"""

import fileinput, sys

to_be_replaced = """.plan:"""

replacement = """.vulnerability: &vulnerability
  - git clone git@gitlab.com:NOT_A_REAL_USER/gitlab-pipeline-scripts.git gitlab-pipeline-scripts/
  - ./scripts/vulnerability-check.sh
  - for i in $(cat report-CRITICAL.json | jq -r .vulnerability_count); do if [[ $i != 0 ]]; then exit 1; fi done
  - for i in $(cat report-HIGH.json | jq -r .vulnerability_count); do if [[ $i != 0 ]]; then exit 1; fi done

.plan:"""
for l in fileinput.input(files = sys.argv[1], mode='rU',inplace=True):
    l = l.replace(to_be_replaced, replacement)
    sys.stdout.write(l)

```