# Getting Started with DreamFactory

[![Netlify Status](https://api.netlify.com/api/v1/badges/f60d78a3-0f5f-4819-bfa4-308d70154a02/deploy-status)](https://app.netlify.com/sites/frosty-goldberg-9f8df9/deploys)

## Configuring Your Development Environment

_The Following Instructions are based on an installation in an Ubuntu Virtual Machine, but this project can be run in any 64bit OS_

### Installing Hugo

We'll use a Vagrant Ubuntu 18.04 VM to run the book website locally:

```
$ git@github.com:dreamfactorysoftware/dreamfactory-book-v2.git
$ vagrant init hashicorp/bionic64
```

Next, open `Vagrantfile` and open guest port `1313`, pointing it to the host port of `8080` (or any other open port):

```
config.vm.network "forwarded_port", guest: 1313, host: 8080
```

Now start the VM:

```
$ vagrant up
```

Next, install the *extended* version of Hugo. This is important. Go [here](https://github.com/gohugoio/hugo/releases) and find the download that looks like `hugo_extended_0.83.1_Linux-64bit.tar.gz`. Of course the version number will be different, but the string `extended` should be in the file name, and you should download the Linux version.

```
$ vagrant ssh
$ sudo apt update
```

Next, download the latest **extended** version of Hugo from [here](https://github.com/gohugoio/hugo/releases). Save this to the virtual machine shared volume. Then `ssh` into your vm, create a hugo directory `mkdir hugo`, move the file there, and extract it using:

```
tar -xf hugo_extended_0.82.0_Linux-64bit.tar.gz
```

Then install Hugo to your `/usr/bin` directory:

```
sudo install hugo /usr/bin
```

You should see the +extended suffix when checking the version with:

```
hugo version
hugo v0.83.1-5AFE0A57+extended linux/amd64 BuildDate=2021-05-02T14:38:05Z VendorInfo=gohugoio
```

### Regarding node and npm

You will need a Node version above 6 for the required npm packages. An easy way to update is using the `n` package:

```
npm cache clean -f
sudo npm install -g n
sudo n stable
```

### Cloning and Booting up the Web Server

Return to your VM home directory and clone the book repository:

```
git clone git@github.com:dreamfactorysoftware/dreamfactory-book-v2.git
```

Enter the project directory and get local copies of the project using:

```
git submodule update --init --recursive
```

Finally, use npm to install the necessary packages:

```
sudo npm install -D autoprefixer
sudo npm install -D postcss-cli
sudo npm install -D postcss
```

Start the Hugo server using `hugo server --bind 0.0.0.0` and go to http://localhost:8080 on your host machine to see the site. 

## DreamFactory Styling:

The scss variables can be changed in `assets/scss/_variables_project.scss`.

* Primary Color: `$primary: #6666CC;`
* Tip Success Color: `$success: #41BA83;`
* Tip Warning Color: `$warning: #E7C000;`
* Tip Danger Color `$danger: #CC0202;`

The font is lato:

```
$google_font_name: "Lato";
$google_font_family: "Lato:300,300i,400,400i,700,700i";
```

### Favicons

* Favicons are located in static/favicons

### Footer

* Changes can be made in the partials folder `themes/docsy/layouts/partials/footer.html`

### Homepage

* The homepage is in `_index.html`, located in the root of the `content/en` directory. You can use the following layout to structure your page:

```
/// Hero Section (Full View Height)
{{< blocks/cover title="Main Title" height="full" color="<color></color>" >}}
html here
{{< /blocks/cover >}}


{{% blocks/lead color="primary" %}}
 // Information Blurb
{{% /blocks/lead %}}

{{< blocks/section color="dark" >}}
{{% blocks/feature title="<Title>" %}}
 // Left Third Content
{{% /blocks/feature %}}


{{% blocks/feature icon="<font awesome icon>" title="<title>" url="<url>" %}}
 // Center Third Content
{{% /blocks/feature %}}


{{% blocks/feature icon="<font awesome icon>" title="<title>" url="<url>" %}}
// Right Third Content
{{% /blocks/feature %}}

{{< /blocks/section >}}
```

### Images

* Images should be stored in the `static` folder, and then can be called from the path after that. For example: `<img src=“/images/02/lb-ha-diagram.png” width=“800”>`

* Note the homepage background image defaults to a darker gradient being applied to the image. This can be removed by deleting the `td-overlay--dark` class from cover.html

### Internal Links

* Use the automatically generated slugs when linking to other pages:
`[Chapter 1. Introducing REST and DreamFactory](./introducing-rest-and-dreamfactory/)`
Slugs are hyphonated lowercase versions of the folder names.

### Linking to the gihub repository

* Make changes in the config.toml file git github_repo (docs repo) and github_project_repo (dreamfactory itself)

### Logos

* Logos need to be in svg format and saved to themes/docsy/assets/icons.
Note that you will most likely need to play around with the height, width, and viewbox in the `<svg>` tag in the inspector until you have something thats fits nicely.

### Making Alerts

```
{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}
```

### Navbar Changes

* Navbar changes (internal links)can be made by going to the relevant root _index.md file (eg content/en/about) and adding a title and linkTitle.

```
---
title: Getting Started With DreamFactory
linkTitle: Home
menu:
  main:
    weight: 10
---
```

`weight` corresponds to the order of tabs from the left.

* External hyperlinks can be added to the navbar by adding the following in the config.toml file

```
[[menu.main]]
    name = "<title of link>"
    weight = 50 
    url = "https://<site>"
    pre = "<i class='fas fa-link'></i>"
```

`pre` will show on the left of the link, `post` will show on the right of the link

* The default navbar setting on the homepage is to match the background color until you scroll down 1 viewport, this can be removed by deleting the `td-navbar-cover` class from navbar.html

## Hosting on Netlify

* On netlify, after choosing "New site from Git" and selecting your repo, you will need to apply the following deploy settings:
1. The build command should specify `cd themes/docsy && git submodule update -f --init && cd ../.. && hugo`
2. In advanced build settings you will need two new variables:

```
HUGO_VERSION 0.83.1
HUGO_ENV production
```

* Make sure that your package.json has the following dependencies in it:

```
"devDependencies": {
    "autoprefixer": "^9.8.6",
    "postcss-cli": "^8.0.0",
    "postcss": "^8.0.0"
  }
```

`postcss` and `postcss-cli` should be set to versions 8 or higher.

## Troubleshooting

As you run the website locally, you may run into the following error:

```
➜ hugo server

INFO 2021/01/21 21:07:55 Using config file: 
Building sites … INFO 2021/01/21 21:07:55 syncing static files to /
Built in 288 ms
Error: Error building site: TOCSS: failed to transform "scss/main.scss" (text/x-scss): resource "scss/scss/main.scss_9fadf33d895a46083cdd64396b57ef68" not found in file cache
```

This error occurs if you have not installed the extended version of Hugo.

