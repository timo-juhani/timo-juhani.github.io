+++
title = 'Bare Minimum Git'
date = 2024-02-07T17:38:04+02:00
draft = false
+++

I had a recurring annoyance of running Gitlab instances in the lab. No matter whether I used Docker or just normal Gitlab instance at some point it broke or didn't want to upgrade without grief. Frankly, I don't have time to fix broken tools. Instead I need a hammer that is maintenance free. I got back to basics by implementing a bare minimum Git setup like this.

```
# Server
# Create a root folder for all projects
mkdir ~/projects
cd projects
mkdir sampleproject.git
cd sampleproject.git
git init --bare --shared=group

# Client
git clone ssh://<user>@<ip>/home/<user>/sampleproject.git
cd sampleproject
touch README.md
git add README.md
git commit -m "add file"
git push origin main
```

That's it. Another client can clone the repo normally and pull / push changes whenever needed.  

Mimimize. minimize. minimize.
