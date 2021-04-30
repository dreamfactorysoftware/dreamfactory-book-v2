# Setting Up a Local Development Environment

_The Following Instructions are based on an installation in an Ubuntu Virtual Machine, but this project can be run in any 64bit OS_

## Installing Hugo

1. Your virtual machine should have guest port 1313 open. In the case of Vagrant, the following line in your VagrantFile should be uncommented and edited.
`config.vm.network "forwarded_port", guest: 1313, host: 8080`
2. Download the latest **extended** version of Hugo from [here](https://github.com/gohugoio/hugo/releases). Save this to the shared volume of your vm.
3. `ssh` into your vm, create a hugo directory `mkdir hugo`, move the file there, and extract it using `tar -xf hugo_extended_0.82.0_Linux-64bit.tar.gz` (filename may not be the same).
4. Install Hugo to your `/usr/bin` directory. `sudo install hugo /usr/bin`
5. You should see the +extended suffix when checking the version with `hugo version`.

## Regarding node and npm

* Please note that you will need a node version above 6 for the required npm packages. An easy way to update is using the `n` package:
```
npm cache clean -f
sudo npm install -g n
sudo n stable
```

## Cloning and Booting up the Web Server

1. Move to the directory you want to download the package to and then:
`git clone git@github.com:dreamfactorysoftware/dreamfactory-book-v2.git`
2. Move into the newly created directory and get local copies of the project using:
`git submodule update --init --recursive`
3. npm install the necessary packages
```
sudo npm install -D autoprefixer
sudo npm install -D postcss-cli
sudo npm install -D postcss
```

Start the Hugo server using `hugo server` and go to localhost:1313 to see the site. If you are unable to view the site, shutdown the server and restart using `hugo server --bind 0.0.0.0`

## DreamFactory Styling:

Primary Color: `$primary: #6666CC;`
Tip Success Color: `$success: #41BA83;`
Tip Warning Color: `$warning: #E7C000;`
Tip Danger Color `$danger: #CC0202;`

Font is lato:

```
$google_font_name: "Lato";
$google_font_family: "Lato:300,300i,400,400i,700,700i";
```

## Making Changes to dreamfactory-book-v2

* scss variables can be changed in assets/scss/_variables_project.scss

### Footer

* Changes can be made in the partials folder `themes/docsy/layouts/partials/footer.html`

### Homepage

* The homepage is the _index.html localted in the root of the `content/en` directory. You can use the following layout to structure your page:
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

