# Hombredequeso Blog Source Data

This repository contains the data for the hombredequeso.github.com blog.
Software Development Blog, using jekyll/github pages.

# Usage
Current update process uses docker to run the static website locally.
See https://github.com/Starefossen/docker-github-pages

Current update process is to clone to local machine, cd to blog base directory (containing \_config.yml) and run:
```
docker run -it --rm -v "$PWD":/usr/src/app -p "4000:4000" starefossen/github-pages
```

If on windows:
```
docker run -v %cd%:/usr/src/app -p "4000:4000" starefossen/github-pages
```

A variation of github-pages docker image which serves up the \_drafts pages as if they were in \_posts is available in the dev/ directory. To use:
```
cd dev
docker build --tag hombredequeso/github-pages:latest .
cd ..
docker run -it --rm -v "$PWD":/usr/src/app -p "4000:4000" hombredequeso/github-pages:latest
```

Watch dev website at [http://localhost:4000]

# License

The following directories and their contents and any derivates of the content (e.g. rss feed) are Copyright Mark Cheeseman. You may not reuse anything therein without my permission:

* _posts/
* images/
* _drafts/

All other directories and files are MIT Licensed. 
