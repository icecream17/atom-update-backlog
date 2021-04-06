See https://github.com/atom/atom/issues/22129

Let's try to move a bunch of repos to __Has update available__, and then update them at all once.

Here, a language has _updated_ when its' tree-sitter dependency is on version 19+

### Already updated repos

Shouldn't have anything yet

- https://github.com/atom/language-javascript (already merged, oops. the version didn't update though so atom still works)

### Has update pr available

- https://github.com/atom/atom (The main repository as well, even though not a language - https://github.com/atom/atom/pull/22130)

### Needs update pr

- https://github.com/mr-martian/language-apertium
- https://github.com/atom/language-python
- https://github.com/atom/language-ruby
- https://github.com/atom/language-html
- https://github.com/atom/language-c
- https://github.com/atom/language-css
- https://github.com/atom/language-go
- https://github.com/atom/language-shellscript
- https://github.com/atom/language-typescript
- https://github.com/atom/language-json

### Can't update because the tree-sitter dependency needs to update first

- https://github.com/atom/language-java

### Doesn't have any tree-sitter dependency for some reason

Most of these are hare because they haven't been updated in years

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
