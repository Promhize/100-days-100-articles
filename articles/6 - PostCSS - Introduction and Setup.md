---
title: PostCSS - Introduction and Setup
published: true
description:
tags: frontend, webdev, css,
cover_image: https://cdn-images-1.medium.com/max/2000/1*m3vlxneAo50hBQYOZbT_ug.jpeg
---

In this article, I will quickly walk you through what PostCSS is and how to set it up.

So I mentioned PostCSS to a developer and he did not know what it was. It’s such a great tool, that I wonder how it’s managed to remain quite hushed since it‘s release over a year ago.

PostCSS allows you to write CSS much more conveniently. Plain and simple. It allows you write more convenient syntax, future CSS, LESS, SASS, etc. Yes, SASS. All those cool things you can do in your `.scss` files, you can do with a PostCSS setup. PostCSS itself doesn’t do anything per se, all of it’s features comes from it’s numerous plugins. These plugins are written by different developers from around the world.

To get setup, you need to have Node and npm installed.

Run `node -v` to check if you have Nodejs installed. If you don’t, head over to nodejs.org to download it, npm comes bundled with Nodejs.

Next step, create an empty folder on your PC (you can name it `postcss_intro`. Open a terminal and make sure it’s pointing at the folder you just created. Next, run `npm init` and follow the prompt, just keep hitting the `enter` key till you get the prompt below,

```
Is this ok? (yes)
```
Type y and hit enter. This will create a package.json file for you within that folder.

Next up, run
```
npm i -D gulp gulp-postcss
```

This will install the gulp and gulp-postcss modules and save them as a dev dependency within the folder you created.

Next up create a folder named src within the postcss_intro folder, or whatever name you might have given it. Create another folder named build also at the top level directory of postcss_intro.

You should now have a directory that looks like this,

![Demo folder snapshot](https://cdn-images-1.medium.com/max/2000/1*54lVC6Nf940iPKM6TjBtVA.png)

Next up, create a Javascript file named `gulpfile.js`. This is where all our settings for running Gulp and PostCSS will go. 😃

Copy the code snippet below and paste it in the gulpfile.js

```javascript
const gulp = require("gulp")
const postcss = require("gulp-postcss")

gulp.task("css", function() {
  const processors = [
    //postcss plugins will go here
  ]
  return gulp
    .src("./src/*.css")
    .pipe(postcss(processors))
    .pipe(gulp.dest("./build"))
})
```

What this does is use the gulp module to load all css files in the `src/` directory, feeds the loaded file to PostCSS and it’s plugins, before finally saving it in the `build/` directory.

Next up, open the `package.json` file. It should look like this

```javascript
{
  "name": "postcss_intro",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp": "^3.9.1",
    "gulp-postcss": "^7.0.0"
  }
}
```

Add the snippet below, add it above `"test": "echo \"Err`…

```javascript
"postcss": "gulp css",
```

Your `package.json` file should now look like this

```javascript
{
  "name": "postcss_intro",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "postcss": "gulp css",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp": "^3.9.1",
    "gulp-postcss": "^7.0.0"
  }
}
```
Next up, create a `master.css` file in the `src` folder, open and add some CSS to it, you can add this,

```
html {
  transform: translate(0%);
}
```

Go to your terminal, pointing at the postcss_intro folder and run

```
npm run postcss
```

You should get an output that looks like this,

```
[19:58:56] Starting 'css'...
[19:58:56] Finished 'css' after 29 ms
```

Check your build directory and you should find a shiny new `master.css` created by gulp.

We have successfully gotten gulp to take our CSS file, feed it to PostCSS and save it in the `build/` directory.

To actually process our CSS file, we will install and add some PostCSS plugins to our gulp build process. From your terminal, run

```
npm i -D autoprefixer

```

When that’s done, open the `gulpfile.js` file again and edit it to look like this

```javascript
const gulp = require("gulp")
const postcss = require("gulp-postcss")
const autoprefixer = require('autoprefixer') //new line

gulp.task("css", function() {
  const processors = [
    autoprefixer //new line
  ]
  return gulp
    .src("./src/*.css")
    .pipe(postcss(processors))
    .pipe(gulp.dest("./build"))
})
```

We have added autoprefixer as a plugin, that will work on the CSS , when it’s fed to PostCSS.

Next run, `npm run postcss again`, you should get the same output as before, only now, when you check the generated `master.css` file in `build`, you would see property rules with prefixes, automatically added.

```
html {
  -webkit-transform: translate(0%);
          transform: translate(0%);
}
```
That’s it. That’s the setup.

Let’s add another plugin, the **responsive-type** plugin. You can install it by running

```
npm i -D postcss-responsive-type
```

Just as we did for autoprefixer, we need to add the **responsive-type** plugin to our `gulpfile.js`. Once more, open the `gulpfile.js` and edit it to look like this,

```javascript
const gulp = require("gulp")
const postcss = require("gulp-postcss")
const autoprefixer = require("autoprefixer")
const responsiveType = require("postcss-responsive-type") //new line

gulp.task("css", function() {
  const processors = [autoprefixer, responsiveType] //new line
  return gulp
    .src("./src/*.css")
    .pipe(postcss(processors))
    .pipe(gulp.dest("./build"))
})
```

Responsive type allows you to write fluid size values that I wrote about in my [completely responsive CSS sizes article](https://dev.to/promhize/completely-responsive-css-values-no-not-media-queries-d4l). So instead of manually writing

```
html {
  font-size: 14px + 10 * ((100vw - 769px) / 2048px);
}
```

You can write,

```
html {
  font-size: responsive;
  min-font-size: 14px;
  max-font-size: 24px;
  lower-font-range: 769px;
  upper-font-range: 2048px;
}
```

which is much more readable. So go to your master.css in the src/ directory, and edit it to look like this,

```
html {
  transform: translate(0%);
  font-size: responsive;
  min-font-size: 14px;
  max-font-size: 24px;
  lower-font-range: 769px;
  upper-font-range: 2048px;
}
```

Run `npm run postcss` again and you should have a `build/master.css` file that looks like this,

```
html {
  -webkit-transform: translate(0%);
          transform: translate(0%);
  font-size: calc(14px + 10 * ((100vw - 769px) / 1279));
}
@media screen and (min-width: 2048px) {
  html {
    font-size: 24px;
  }
}
@media screen and (max-width: 769px) {
  html {
    font-size: 14px;
  }
}
```

Isn’t that just awesome.

Running `npm run postcss` everytime we make a change is boring, we are too lazy for that sort of thing. So we will set a gulp task runner, to watch and automatically transform and build our CSS files, anytime we save them.

Fire up your `gulpfile.js` file for the last time and add this snippet at the bottom,

```javascript
gulp.task("watch", function() {
  gulp.watch("./src/*", ["css"])
})
```

Your gulpfile should now look like this,

```javascript
const gulp = require("gulp")
const postcss = require("gulp-postcss")
const autoprefixer = require("autoprefixer")
const responsiveType = require("postcss-responsive-type")

gulp.task("css", function() {
  const processors = [autoprefixer, responsiveType]
  return gulp
    .src("./src/*.css")
    .pipe(postcss(processors))
    .pipe(gulp.dest("./build"))
})

gulp.task("watch", function() {
  gulp.watch("./src/*", ["css"])
})
```

Next add the snippet of code below to your `package.json` file, below the `"postcss": "gulp css"` line
```
"postcss-watch": "gulp watch",
```
You should now have a `package.json` file that looks like this,

```javascript
{
  "name": "postcss_intro",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "postcss": "gulp css",
    "postcss-watch": "gulp watch",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "autoprefixer": "^7.1.5",
    "gulp": "^3.9.1",
    "gulp-postcss": "^7.0.0",
    "postcss-responsive-type": "^1.0.0"
  }
}
```

With this, you can now run

```
npm run postcss-watch
```

in your terminal once, and gulp would be watching your `src/master.css` file and automatically transforming it, whenever you make a change and save it. Make sure your terminal is showing something like this,

![Gulp terminal process snapshot](https://cdn-images-1.medium.com/max/2000/1*mfCv6nlnu7fWLnH08ypT7g.png)

Now, make a change to the src/master.css file. Say, change the min-font-size to 16px, your terminal will output something like

![Gulp terminal process snapshot](https://cdn-images-1.medium.com/max/2000/1*dXvR-Tc5cf_pUnaNM9XllA.png)

Open your `build/master.css` and you should see the your changes reflecting. Anytime you want to stop the process, go to the terminal where the gulp task is running and hit `CTRL+C`, type `yes` and hit enter.

PostCSS has several more plugins, much more powerful, with very useful functionalities. You can view them at https://postcss.parts.

My specific build process, which hasn’t changed much in over a year that I’ve been using PostCSS includes plugins such as,

*postcss-inline-media
*css-mqpacker
*postcss-simple-vars
*postcss-media-variables
*postcss-responsive-type
*autoprefixer
*rucksack

My favourite is the combination of `postcss-media-variables` and `postcss-inline-media`, makes CSS like this possible

```
.sidebar {
  width: @ml 30%;
}
```

That rule now will only be applied to larger screens, as I have defined this somewhere else,

```
$ml: (min-width: 769px);
```

If you haven’t used PostCSS previously, I hope you explore its numerous plugins and give it a trial. There are numerous plugins that bring SASS functionality to PostCSS, https://www.postcss.parts/tag/sass . Companies like Twitter, Alibaba, use PostCSS, you should give it a shot if you haven’t.

_[This article was originally published by me on medium on the 17th of October.](https://medium.com/@promisetochi/day-2-postcss-introduction-and-setup-2db010352864)_
