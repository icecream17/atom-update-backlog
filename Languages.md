See https://github.com/atom/atom/issues/22129

Let's try to move a bunch of repos to __Has update available__, and then update them at all once.

Here, a language has _updated_ when its' tree-sitter dependency is on version 19+

Progress:

```js
Currently the "step" totals are:
  3 steps: 1    // needs dependency to update first
  2 steps: 5    // needs update
  1 steps: 3    // has update ready, but it fails
  done!!!: 4    // has update ready; or, already updated

16 steps remaining, 9 not done
```

### Already updated repos

These updated versions will not syntax highlight until https://github.com/atom/atom/pull/22130 is merged, which in turn requires all the repos to be updated

- https://github.com/atom/language-javascript (https://github.com/atom/language-javascript/pull/692)
- https://github.com/atom/language-css (https://github.com/atom/language-css/commit/f889acb7f6f1ee99e0689c62fe9236e201aff695)
- https://github.com/atom/language-go (https://github.com/atom/language-go/commit/8095b52af738a43e028ff559d3a75d5ae40a6ed9)


### Has update

- https://github.com/atom/language-json (https://github.com/atom/language-json/pull/84)

### Has update, but it fails

- https://github.com/atom/atom (The main repository as well, even though not a language - https://github.com/atom/atom/pull/22130)
- https://github.com/atom/language-ruby (https://github.com/atom/language-ruby/pull/296)
- https://github.com/atom/language-html (https://github.com/atom/language-html/pull/252)

### Needs update

- https://github.com/mr-martian/language-apertium
- https://github.com/atom/language-python
- https://github.com/atom/language-c
- https://github.com/atom/language-shellscript
- https://github.com/atom/language-typescript

### Can't update because the tree-sitter dependency needs to update first

In this case though, maybe it would be better to switch dependencies completely.

- https://github.com/atom/language-java

### Doesn't have any tree-sitter dependency for some reason

Most of these are here because they haven't been updated in years

These are ignored in the progress count since the issue doesn't apply

- https://github.com/atom/language-php
- https://github.com/atom/language-sass
- https://github.com/atom/language-xml
- https://github.com/atom/language-perl
- https://github.com/atom/language-ruby-on-rails
- https://github.com/atom/language-todo
- https://github.com/atom/language-less
- https://github.com/atom/language-csharp
- https://github.com/atom/language-clojure
- https://github.com/atom/language-hyperlink
- https://github.com/atom/language-coffee-script (outdated - it's now coffeescript)
- https://github.com/atom/language-make
- https://github.com/atom/language-sql
- https://github.com/atom/language-mustache
- https://github.com/atom/language-git
- https://github.com/atom/language-yaml
- https://github.com/atom/language-puppet
- https://github.com/atom/language-pegjs
- https://github.com/atom/language-objective-c
- https://github.com/atom/language-toml
- https://github.com/atom/language-text
- https://github.com/atom/language-property-list
- https://github.com/atom/language-source

### Note

Repos that are not owned by atom are probably not listed.

If you know about one of these repos please open an issue or pr.

`atom-ide-<language>` repos are also not here.
