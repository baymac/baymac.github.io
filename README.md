# baymac.github.io

## Build Locally

All steps for Mac OS Catalina

### Install Ruby:

Ruby also comes with Mac by default. You might not want to use/modify that version. If you want use it skip this step.

```
brew install ruby
```

Add brew ruby path to your shell config:

```
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
```

### Local Install bundler and Jekyll:
 
 ```
 gem install --user-install bundler jekyll
 ```

Add gem path to shell config:

```
echo 'export PATH="$HOME/.gem/ruby/X.X.X/bin:$PATH"' >> ~/.zshrc
```

where X.X.X is your ruby version. Find it with `ruby -v`

Verify if gem path is set properly with:

```
gem env
```

### Install github pages dependencies

Go to the Root of this repo and run:

```
bundle install
```

### Serve the static files on localhost

```
bundle exec jekyll serve
```

By default server runs on port 4000. You can change it with `--port` option.

## Acknowledgement

This blog uses template by https://github.com/johno/pixyll. Some design inspirations are from https://github.com/taniarascia/taniarascia.com/.
