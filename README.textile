# Hombredequeso Blog Source Data

This repository contains the data for the hombredequeso.github.com blog.
Software Development Blog, using jekyll/github pages.
Overall format and styling originally taken from the blog of Tom Preston-Werner, http://github.com/mojombo/jekyll

# Usage
Current development process is to clone to local machine, cd to blog base directory (containing \_config.yml) and (formatted for windows) run:
```
docker run -v %cd%:/usr/src/app -p "4000:4000" starefossen/github-pages
```

Watch dev website at [http://localhost:4000]

# License

The following directories and their contents are Copyright Mark Cheeseman. You may not reuse anything therein without my permission:

* _posts/
* images/
* _drafts/

All other directories and files are MIT Licensed. 
