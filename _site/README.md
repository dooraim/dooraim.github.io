# README

Guarda come spunto https://krinkinmu.github.io/

Cambia il tema con https://github.com/riggraz/no-style-please

## Esecuzione in Locale

Per poter eseguire no-style-please in locale bisogna eseguire i comandi seguenti:

```bash
$ brew install chruby ruby-install xz
$ sudo gem install rouge -v 3.30.0
$ sudo gem install sass-embedded -v 1.58.3
$ sudo gem install -n /usr/local/bin jekyll
$ git clone https://github.com/riggraz/no-style-please
$ cd no-style-please
$ sudo bundle install
$ bundle exec jekyll serve
Configuration file: /Users/nicoorlando/temp/no-style-please/_config.yml
            Source: /Users/nicoorlando/temp/no-style-please
       Destination: /Users/nicoorlando/temp/no-style-please/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
       Jekyll Feed: Generating feed for posts
                    done in 0.22 seconds.
 Auto-regeneration: enabled for '/Users/nicoorlando/temp/no-style-please'
    Server address: http://127.0.0.1:4000/no-style-please/
  Server running... press ctrl-c to stop.
```

Collegarsi a http://127.0.0.1:4000/no-style-please/ per vedere il sito in locale.
