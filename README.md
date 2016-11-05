# Introduction
Software Development Blog, using jekyll/github pages.
# Usage
Current development process is to clone to local machine, cd to blog base directory (containing \_config.yml) and (formatted for windows) run:
```
docker run -v %cd%:/usr/src/app -p "4000:4000" starefossen/github-pages
```

Watch dev website at [http://localhost:4000]

