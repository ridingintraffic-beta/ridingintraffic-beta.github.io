---
draft: false
title: 'github one liner and powershell curl'
categories: [ daily ]
tags: [ git ]
date: 2019-04-09-T08:08:50-04:00
---

# Github releases
There is a nice way to get the latest release from a github repo. as long as it gets tagged as release.
I like to be able to pull down the latest version of a release for specific code in automated builds.
This helps that. 

Use `curl` to get the JSON response for the latest release
Use `grep` to find the line containing file URL
Use `cut` and tr to extract the URL
Use `wget` to download it
```
curl -s https://api.github.com/repos/hashcat/hashcat/releases/latest | 
grep "browser_download_url" | 
cut -d : -f 2,3 | 
tr -d \" | 
wget --no-check-certificate -qi -
```
gist credit goes to
[https://gist.github.com/steinwaywhw/a4cd19cda655b8249d908261a62687f8#file-one-liner-to-download-the-latest-release-from-github-repo-md](https://gist.github.com/steinwaywhw/a4cd19cda655b8249d908261a62687f8#file-one-liner-to-download-the-latest-release-from-github-repo-md "file-one-liner-to-download-the-latest-release-from-github-repo-md")

# powershell curl
powershell can call webpages directly and return it much like curl 
```
(Invoke-WebRequest -Uri "https://github.com/raw/something" -SkipCertificateCheck).content | Out-File -FilePath /tmp/something.txt
```