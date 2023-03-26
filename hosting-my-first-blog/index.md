# Hosting my first blog for FREE using Hugo and GitHub Pages :smile:




After conducting extensive research and considering various options, I decided to use Hugo as my Static Site Generator and Hugo themes for my blog

My plan is to host the static content on Github Pages under username.github.io and host the static server source code in another repository named username_blog_src. Finally, I will create a Github action to publish the content automatically.


Here are the steps I followed to start my blog:

1. Install Hugo
2. Create a first static Hugo website
3. Choose a theme for my blog
4. Create Github repositories for public content and source code
5. Automate the process using GitHub Actions
6. Publish my first blog

## Installing Hugo

Hugo is a static HTML and CSS website generator, written in GO, and you can publish content using the Markdown language. Since I use Obsidian for my notes, I decided to publish my blog content using Markdown.

I use MacOs so I have decided to use brew to install hugo (https://gohugo.io/installation/macos/).

Run the following  command in terminal: 

```sh
brew install hugo
```

Once Hugo is installed, you can use the command line by running hugo. Try running hugo --help to list all the options you can use.

## Create a first static hugo website

I created two repositories in git:

- username.github.io - this repository will be used to host the public contect
- username_blog_src - this repository with be used to host the source code and it will be private repo

{{< admonition tip "Tip" false >}}
Please switch username with your github username
{{< /admonition >}}

run the following in terminal

```bash
hugo new site username.github.io
```

And voilà! The new static site was created.

## Choose a theme for my blog

Now we come to the hardest part: choosing the theme for the blog. As I plan to write a tech blog, I focused more on blog themes with a dark mode. I evaluated the following:

- PaperMod - https://github.com/adityatelange/hugo-PaperMod (GitHub stars: 4432)
- Coder - https://github.com/luizdepra/hugo-coder (GitHub stars: 2026)
- LoveIt - https://github.com/dillonzq/LoveIt (GitHub stars: 2279)

After evaluating these themes, I decided to choose LoveIt because it provides both a dark and light theme, is more colorful, and fits my personality and writing style. On top of all, I love it!

Winner: LoveIt - https://github.com/dillonzq/LoveIt (GitHub stars: 2279)

It was time to install the theme. I added the theme as a submodule in my source code by running:

``` bash
cd username.github.io/
git submodule add https://github.com/dillonzq/LoveIt themes/LoveIt
```

Now you need to add the following to the config.toml file and replace baseURL, title, author.name, and author.email:

```toml
baseURL = "https://username.github.io/"

# Change the default theme to be use when building the site with Hugo
theme = "LoveIt"

# website title
title = "My Blog"

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

```bash
├── username.github.io
│   ├── archetypes
│   ├── assets
│   ├── content
│   ├── data
│   ├── layouts
│   ├── public
│   ├── resources
│   ├── static
│   ├── themes
│   ├── config.toml
└──  ── .gitignore
```

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
## Conslusion

In conclusion, hosting your blog on Github Pages using Hugo and LoveIt theme is an easy and effective way to create a professional-looking website. The simplicity of Hugo's static site generator, combined with the customization options of the LoveIt theme, make it easy to create a unique and visually appealing blog. Additionally, Github Pages' free hosting service and built-in version control make it an accessible option for bloggers of all levels of experience. Overall, this combination provides a straightforward and powerful solution for creating a personal or professional blog on the web.









