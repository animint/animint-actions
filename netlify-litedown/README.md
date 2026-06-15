This action is for R package documentation web sites created using litedown.
Your yml code should do as in the example below.

* Checkout with `fetch-depth: 0` so that base branch is available, for getting links to changed web pages in the PR comment.
* Install R and dependencies of your package.
* Build, install, build site for your package.
* Call this action.

```yml
name: build site

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  litedown:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 0
      - name: Setup
        uses: eddelbuettel/github-actions/r-ci@master
      - name: Dependencies
        run: ./run.sh install_all
      - name: install package and build site
        run: |
          R CMD build .
          R CMD INSTALL *.tar.gz
          R -e 'litedown::fuse_site("site")'
        shell: bash
      - name: netlify deploy
        uses: animint/animint-actions/netlify-litedown@main
        with:
          netlify_auth_token: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          netlify_site_id: ${{ secrets.NETLIFY_SITE_ID }}
          path: site
          pr_comment_token: ${{ secrets.GITHUB_TOKEN }}
```
