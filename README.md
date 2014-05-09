# popit.poplus.org

The promotional site for the Poplus component, PopIt. Compiled using Jekyll and hosted by Github at http://popit.poplus.org

## Local development

The templates in `_layouts` and the majority of the styles in `assets/sass/` should be kept in sync with other poplus.org projects like https://github.com/mysociety/writeit.poplus.org and https://github.com/mysociety/mapit.poplus.org

Styling changes specific to the PopIt site should be made in `assets/sass/_popit-styles.scss`.

Compile the Sass files using [(docs)](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#using_sass):

```shell
sass --watch assets/sass:css
```

## Running a local web server

You can preview your changes without pushing to Github by running a local Jekyll web server.

First, install Jekyll, as per the Jekyll docs, then go into the project folder and start a local server:

```shell
gem install jekyll
cd popit.poplus.org
jekyll serve --watch --baseurl ''
```

The `--baseurl` option is part of [an elegant hack](http://jekyllrb.com/docs/github-pages/#project_page_url_structure) to replicate the Github Pages hosting structure locally.

As long as the server is running, your site will be available at http://0.0.0.0:4000. Local changes will be reflected automatically, thanks to the `--watch` flag.

Remember, when you’re editing links or paths in the HTML, to use the `{{ site.baseurl }}` template tag for internal links. Take a look in `_includes/html-head.html` for an example.

## Deploying changes

The site is hosted on [Github Pages](https://help.github.com/articles/creating-project-pages-manually#add-content-and-push). All changes should be made on the `gh-pages` branch.

Your changes will then be visible at http://popit.poplus.org.
