# Hosting my blog

After long time researching and going through different solutions and also recommandations from Davy I have decided to chose Hugo as my Static Site Generator and Hugo themes for my blog.

The idea is to host the static content in github pages: username.github.io and host static server source code in another repo called username_blog_src.

In the end I will write a github action which will publish the content automaticaly.

I have to do the following steps to start my blog:

1. Install hugo
2. Create a first static hugo website
3. Choose a theme which I will use for my blog
4. Create github repositories for public contenct and source code
5. Automate the process using GitHub Actions
6. Publish my first blog

## Installing Hugo

Hugo is a static HTML and CSS website generator, it's writen in GO and you can publish content using markdown language. Because I use Obsidian for my notes, I decided to publish also my blog content using Markdown.

I use MacBook Pro M1 notebook so I have decided to use brew to install hugo (https://gohugo.io/installation/macos/).

Run the following  command in terminal: 

```sh
brew install hugo
```

Note: hugo--0.111.3.arm64_ventura.bottle.tar.gz was installed

When hugo is installed you can use command line: hugo. Try it with hugo --help to list all the options that you can use.

## Create a first static hugo website

First I create two repositories in git :

	- username.github.io - this repository will be used to host the public contect
	- username_blog_src - this repository with be used to host the source code and it will be private repo

run the following in terminal

```bash
hugo new site username.github.io
```


Tadaa!!!. The new static site is created.

## Choose a theme which I will use for my blog

Now we come to the hardest part. Choosing the theme for the blog. Because I will write a tech blog  I was focused more in blog themeses with dark theme. I evaluated the following:

- PaperMod - https://github.com/adityatelange/hugo-PaperMod (GitHub stars: 4432)
- Coder - https://github.com/luizdepra/hugo-coder (GitHub stars: 2026)
- LoveIt - https://github.com/dillonzq/LoveIt (GitHub stars: 2279)

I decided to chose LoveIt because it provides a a dark and light theme, it's more colorfull and it fits my personality and way of writing. And on top of all I Love IT.


Winner:  - LoveIt - https://github.com/dillonzq/LoveIt (GitHub stars: 2279)

Time to install it.  I added the theme as a submodule on my source code.
``` bash
cd username.github.io/
git submodule add https://github.com/dillonzq/LoveIt themes/LoveIt
```

Now you have to add the following in config.toml file and replace baseURL, title, and author.name and author.email:

```toml
baseURL = "http://example.org/"

# Change the default theme to be use when building the site with Hugo
theme = "LoveIt"

# website title
title = "My New Hugo Site"

# language code ["en", "zh-CN", "fr", "pl", ...]
languageCode = "en"
# language name ["English", "简体中文", "Français", "Polski", ...]
languageName = "English"

# Author config
[author]
  name = "xxxx"
  email = ""
  link = ""

# Menu config
[menu]
  [[menu.main]]
    weight = 1
    identifier = "posts"
    # you can add extra information before the name (HTML format is supported), such as icons
    pre = ""
    # you can add extra information after the name (HTML format is supported), such as icons
    post = ""
    name = "Posts"
    url = "/posts/"
    # title will be shown when you hover on this menu link
    title = ""
  [[menu.main]]
    weight = 2
    identifier = "tags"
    pre = ""
    post = ""
    name = "Tags"
    url = "/tags/"
    title = ""
  [[menu.main]]
    weight = 3
    identifier = "categories"
    pre = ""
    post = ""
    name = "Categories"
    url = "/categories/"
    title = ""

# Markup related configuration in Hugo
[markup]
  # Syntax Highlighting (https://gohugo.io/content-management/syntax-highlighting)
  [markup.highlight]
    # false is a necessary configuration (https://github.com/dillonzq/LoveIt/issues/158)
    noClasses = false

```

No if you want to have a sneak peak of how your website will look like run:

```bash
hugo server -D
```

Open your browser and search for: 

http://localhost:1313/

## Create github repositories for public contenct and source code

Go to Github, login with your credentials and go to your repositories. Click new and create the following repositories:

- Repository 1:
	- name: username.github.io (It must be the name of your github user)
	- visibility: public
- Repository 2:
	- name: username_blog_src
	- visibility: private

When we created the site hugo creates a folder structure as following:

├── username.github.io
		├── archetypes
	    ├── assets
	    ├── content
	    ├── data
	    ├── layouts
		├── public
		├── resources
		├── static
		├── themes
	    └── config.toml

We need to publish the contents of public folder to our public repo: username.github.io and all others in our source repo (ignoring public folder).

```bash
cd public
git init
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/username/username.github.io.git
git push -u origin main
```

Now we have to push the source code to the second repo:

```bash
cd ..
echo "public/" >> .gitignore
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/username/username_blog_src.git
git push -u origin main
```

## Automate the workflow using github actions

1. Create a secret (PERSONAL_TOKEN) in Github actions with your personal access token (https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

2. Create a new github action in git with the followign content
```
name: Blog CI

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true 
          fetch-depth: 1   

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.PERSONAL_TOKEN }}
          external_repository: username/username.github.io
          publish_branch: main
          publish_dir: ./public
          
```


## Publish my first blog

Run the following command :

```bash
hugo new posts/my-first-post.md
```

Push it to the repo:

```bash
git add .
git commit -m 'commit my first post'
git push
```




#hugo #loveit #blog
