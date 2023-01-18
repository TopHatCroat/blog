# izi Hugo Theme

> 'izi' name come from Croatian spelling of the English work 'eazy'

A fast, minimalist and responsive hugo theme.

![./images/screenshot.png](https://raw.githubusercontent.com/tophatcrpat/izi-hugo-theme/master/images/screenshot.png)

- [Example site](https://martinovic.blog)

Features:

- Fast
- Minimalist
- Responsive
- Archive
- Tags


## 1. Installation


### 1.1 As a Hugo Module (recommended)

> ⚠️ If you installed a [Hugo binary](https://gohugo.io/getting-started/installing/#binary-cross-platform),
> you may not have Go installed on your machine. To check if Go is installed:
> ```
> $ go version
> ```
>  Go modules were considered production ready in v1.14. [Download Go](https://golang.org/dl/).

1. From your project's root directory, initiate the hugo module system if you haven't already:

    ```bash
    $ hugo mod init github.com/<your_user>/<your_project>
    ```

2. Add the theme's repo to your `config.yaml`:

    ```yaml
    theme:
       - github.com/tophatcroat/izi-hugo-theme
    ```

### 1.2 As Git Submodule

1. Inside the folder of your Hugo site run:

    ```bash
    $ git submodule add https://github.com/tophatcroat/izi-hugo-theme.git themes/izi
    ```

2. Add the theme's directory to your `config.yaml`:

    ```yaml
   theme: mini
    ```

For more information read the official [setup guide](//gohugo.io/overview/installing/) of Hugo.


## 2. Getting started

After installing the theme successfully it requires a just a few more steps to get your site running.


### 2.1 The config file

Take a look inside the [`exampleSite`](https://github.com/tophatcroat/izi-hugo-theme/tree/master/exampleSite) folder of
this theme. You'll find a file called
[`config.yaml`](https://github.com/tophatcroat/izi-hugo-theme/blob/master/exampleSite/config.yaml).
To use it, copy the [`config.yaml`](https://github.com/tophatcroat/izi-hugo-theme/blob/master/exampleSite/config.yaml)
in the root folder of your Hugo site. Feel free to change the strings in this theme.

> ⚠️ You may need to delete the line: `themesDir: ../../`

### 2.2 Default Content Language

You can set default content language by `defaultContentLanguage`:

```yaml
defaultContentLanguage: en-us
```

Default is `en-gb`. Now support:

- `en-us`: American English
- `en-gb`: English

More about multiple languages: [Multilingual Mode](https://gohugo.io/content-management/multilingual/).

### 2.3 Google Analytics

To enable google analytics, add following to your config file:

- Google Analytics ID: `googleAnalytics: your-google-analytics-id`
- Enable Google Analytics:

    ```yaml
    params:
      enableGoogleAnalytics: true
    ```

### 2.4 Logo and favicon

You can replace the log in the top of each page and favicon with your own images. To do that put your own logo and
favicon into the `images` directory of your website static directory, then named them `avatar.png` and `favicon.ico`.
For example:

```
- content
- static
└── images
    ├── avatar.png
    └── favicon.svg
```

### 2.5 Nearly finished

In order to see your site in action, run Hugo's built-in local server.

```bash
$ hugo server
```

Now enter http://localhost:1313 in the address bar of your browser.

### 2.6 Production

To run in production (e.g. to have Google Analytics show up), run HUGO_ENV=production before your build command.
For example:

```bash
HUGO_ENV=production hugo
```

Note: The above command will not work on Windows. If you are running a Windows OS, use the below command:

```bash
set HUGO_ENV=production
hugo
```


## 3. Optional Configuration

### 3.1 Custom CSS and JS

You can put your custom css and js files to `static` directory, or use remote css and js files which start with
`http://` or `https://`.

For example:

```yaml
customCSS:
  - css/custom.css # local css in `static/css/custom.css`
  - https://example.com/custom.css # remote css
customJS:
  - js/custom.js # local js in `static/js/custom.js`
  - https://example.com/custom.js # remote js
```

### 3.2 Post Summary in Home Page

Set `hiddenPostSummaryInHomePage` to `true` to show the first paragraph on the index page, default is `false`.

For example:

```yaml
hiddenPostSummaryInHomePage: true
```

Alternatively, you can add a `description` in the Front Matter to provide bespoke summary.


### 3.3 Fonts

You can replace default fonts for article titles and content

```yaml
  titleFont: 'https://fonts.googleapis.com/css?family=Unna'
  textFont: 'https://fonts.googleapis.com/css?family=Open+Sans'
```

And also inside your `customCSS` file, for example `static/css/style.css`, add:
```css
:root {
    --title-font: Unna, serif;
    --text-font: 'Open Sans', serif;
}
```
