
# Tech Notes

A simple collection of technical notes that I would rather not forget. 

Built using the brilliant theme `Just the Docs`.

## Reference:
main:
https://pmarsceill.github.io/just-the-docs/

helper:
https://pdmosses.github.io/just-the-docs-tests/
https://pdmosses.github.io/test-nav/docs/Mathjax/

## Usage:

local dev
```
bundle exec jekyll serve
```
and navigate to the listed location. 

You can also run 
```
bundle exec jekyll serve --livereload --port 4001
```
to specify a port number.

After every edit, we can refresh the page to see the updates.

If `bundle exec` isnt working, you might need to reinstall bundle

```
gem uninstall bundler
gem install bundler
```


## Running with Docker:

idk why but the gems and bundler are really annoying to work with. They keep giving me issues. Use docker to fix installation hell:

Simply `cd` into this directory and run
```
docker run -p 4000:4000 -v $(pwd):/site bretfisher/jekyll-serve
```
which will start up a jekyll docker, run the bundle install step, and serve the website to `http://0.0.0.0:4000/tech-notes/`