# README.md

The current blog setup utilizes [GitHub Pages](https://pages.github.com/), which under covers automatically runs the static website generator
[Jekyll](https://jekyllrb.com/) on the pages checked into GitHub.

The current setup contains all blog-related files in the branch `gh-pages` of the repository [sysflow](https://github.com/sysflow-telemetry/sysflow).

For changes it should mostly be fine to just change files directly on github or of course on a local clone (in both cases, don't forget that you have to switch to the `gh-pages` branch). Only if one wishes to be able to test and view changes with a _local_ server, a local setup with ruby and Jekyll is required - there are some small tricks to set this up, so let me know if you are interested in such a setup.

Using the GitHub route, the easiest way to create a new post is like:

- create a new file like `YEAR-MONTH-DAY-title.md` in the folder `_posts/`, e.g., `_posts/2021-07-26-sf-processor-0.3.0-rc2.md`.
  The entry should start with this front-matter YAML for an article with a specific author:
  ```
  ---
  layout: single
  title:  "New release sf-processor 0.3.0-rc2"
  date:   2021-07-28 12:01:25 +0200
  tags:   update
  author_profile: true
  author: Axel Tanner
  ---
  ```
  If the article should remain 'anonymous', switch the `author_profile` off like:
  ```
  ---
  layout: single
  title:  "New release sf-processor 0.3.0-rc2"
  date:   2021-07-28 12:01:25 +0200
  tags:   update
  author_profile: false
  ---
  ```
  We have to consider the tags to use - maybe `update`, `usecase`, `demonstration` and the like?
- I have seeded the author information with some data from bluepages (namely email and image), you might like to add other info or change it by editing [authors.yml](https://github.com/sysflow-telemetry/sysflow/blob/gh-pages/_data/authors.yml)

- edit the content as usual - using GitHub-flavored markdown
- then either check-in the page directly, or maybe as a better workflow, during the check-in select the choice `Create a new branch for this commit and start a pull request`, then a new branch will be created making it easy to create a PR and have somebody else look at the new content before publishing. This process is described in the [GitHub Pages Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll#adding-a-new-post-to-your-site) - but please use the front-matter YAML as shown above.

Keep in mind that after a change in the repository, GitHub has still to run the machinery to generate the pages for the site - which takes like half a minute - so a bit of patience is required until the changes actually show up on the net.

This blog now uses the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) template (freely available with MIT license): it has a nice, clean style and is very feature-rich and customizable - and well documented.
See for its features the [documentation](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/).

The template supports `tags` as well as `categories`, but I would suggest to stick with just `tags` for now - until it really gets a _large_ blog :-)

If you have wishes wrt the design etc, let me know - most things are open for customization, but the documentation is kind of extensive and I now have at least an initial understanding of how the pieces work together.
