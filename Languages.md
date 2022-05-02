> **Notice**: `tree-sitter` is now on v20 instead of v19
>
> [https://github.com/atom/atom/pull/23283#issuecomment-1112509152] shows an easy method to update a language repo,
> here are the instructions along with the gitpod commands I would use:
>
> 0. If using gitpod `nvm install 14` or `nvm install 16` depending on the `lockfileVersion` of `package-lock.json`
>
> 1. Update tree-sitter to v0.20.0
>    ```shell
>    npm install tree-sitter@\*
>    ```
> 2. Manually rebuild each tree-sitter dependency using
>    ```shell
>    cd node_modules/tree-sitter-<language>
>    tree-sitter generate
>    npm rebuild
>    electron-rebuild --version 11.5.0
>    ```
>

Atom update pr: https://github.com/atom/atom/pull/23283<br>
Issue: https://github.com/atom/atom/issues/22129

Let's try to move a bunch of repos to at least __Has update available__, and then update them at all once.

Progress:

```js
Currently the "step" totals are:
  todo: 12 // no pull request to go to v20
  pull: 0  // has pull request
  merg: 0  // pull request merged
  done: 0  // pull request merged and new release happens

36 steps remaining
Languages not owned by atom, like mr-martian/language-apertium, are more optional.
```

### Done (New version released)

Note that incompatible tree-sitter packages (e.g. v20 packages when atom is on v17) will not syntax highlight.

### Merged

### Pr (Has update available)

### Todo

Note that v19 progress is not necessary since tree-sitter packages can be updated directly to v20.

#### On v19

- https://github.com/atom/language-javascript
- https://github.com/atom/language-css
- https://github.com/atom/language-go
- https://github.com/atom/language-c

#### Has v19 update (non-regenerate command)

- https://github.com/atom/language-json (https://github.com/atom/language-json/pull/84)

#### v17

Note: (language-rust-bundled)[https://github.com/atom/atom/tree/master/packages/language-rust-bundled] will be updated with Atom.

- https://github.com/atom/language-ruby
- https://github.com/atom/language-html
- https://github.com/atom/language-python
- https://github.com/atom/language-shellscript
- https://github.com/atom/language-typescript
- https://github.com/atom/atom/tree/master/packages/language-rust-bundled

### Can't* update because the tree-sitter dependency needs to update first

In this case though, maybe it would be better to switch dependencies completely.

Edit: See comment above.

- https://github.com/atom/language-java

### Doesn't have any tree-sitter dependency

Most of these haven't been updated in years

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

### Not owned by atom

Just here for reference; these repos are optional.

- https://github.com/mr-martian/language-apertium

Most repos that are not owned by atom are not listed.
If you know about one of these repos feel free to make a comment, issue, or pr.

> Note: `atom-ide-<language>` repos are also not here
