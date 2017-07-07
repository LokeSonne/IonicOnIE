# IonicOnIE
Hacks to make Ionic run on Internet Explorer 11

## Why run Ionic in IE?

Ionic is a html5 framework for creating mobile apps for iOS, Android and Windows Universal Apps. It does not support IE.

Using Ionic to build desktop web apps is possible, and if you stick to Chrome you will not have many issues. Beware of mobile-centric componets though.

If your users need to run their apps in the currently most despised browser,  Internet Explorer, here is a list of hacks that will make it possible.

The list is not in anyway complete. Feel free to add to it.

## CSS3 and IE

So, a lot of features of CSS3 are not supported in Internet Explorer 11. Keywords like unset and initial will be ignored. That might cause trouble for your Ionic app, since it relies heavily on that.

Ionic also uses flexbox extensively. Flexbox is supported by IE11 but the implementation is buggy.


## PostCSS

[Postcss](https://github.com/postcss/postcss) lets you postprocess your css using a builder like Webpack or Gulp. PostCSS doesn't do much on its own but it lets you use a lot of plugins that solves specific CSS problems.

I have chosen to use Gulp because Ionic has a plugin for that. This lets you use hooks for Ionic-cli commands, e.g. `ionic cordova build` that will run any Gulp task with `ionic:build:after` after the build has completed. Unfortunately there doesn't seem to be a way to use it together with the watcher (`ionic serve`). The hook `ionic:watcher:before` runs the task before the build has started. It can therefore not be used to process the generated main.css in the build folder.

To use PostCSS with Gulp:

`npm install @ionic/cli-plugin-gulp --save-dev`

You should now see `gulpfile.js` in the root of your project. In this file add the following:

```
var postcss = require('gulp-postcss');
var gulp = require('gulp');

gulp.task('somefix', function () {
    var postcss = require('gulp-postcss');
    var processors = [
    ];
    console.log('Gulp finished!')

    return gulp.src('./www/build/*.css')
        .pipe(postcss(processors))
        .pipe(gulp.dest('./www/build/'));
});

gulp.task('ionic:build:after', ['somefix']);
```
## Polyfills

Some issues can be fixed with polyfills. Ionic already includes a number of polyfills but more can easily be added.

**Beware** Using automated fixes like polyfills and PostCSS can lead to other components not rendering properly.

In your `/src/app` folder add a file called `mypolyfills.ts`. Here you import all the needed polyfills.

Example of mypolyfills.ts:

```
import 'polyfill-svg-uri/polyfill-svg-uri';
```

Now, import this in your `main.ts` and you're good to go. Like so:

```
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app.module';
import './mypolyfills';

platformBrowserDynamic().bootstrapModule(AppModule);
```

## Limiting CSS fixes to IE11 only


### ion-item
Issue: ion-item collapses on IE. 

Cause:

Hack:

```
.md {
    ion-item{
        min-height: 8rem;
    }
}
```

### ion-datetime

Warning! Do not attempt to use the ion-datetime component for desktop. It is very much intended for touch based devices. It probably _can_ be made to work on IE, but your users will understandibly hate you.

Issue: Day and month list collapses into one

Cause:

Hack:

```
.md {
    .picker-col{
        max-width: 20rem !important;
    }
}
```

### ion-range

Issue: Too much whitespace between left label and range slider

Cause: The flex property is set to `initial` for the labels. The keyword is not supported by IE and reverts to flex: 1. This result in the two labels and the slider is are in eqaul sized flex boxes.

Hack:

```
{
ion-range{
    .range-slider{
        flex:10;
    }
}
```

### icons

Issue: Icons are not displayed in IE11

Cause: I'll let Anseki explain it, author of the polyfill https://github.com/anseki/polyfill-svg-uri

>The plain SVG without encoding (e.g. Base64, URL encoding, etc.) that is written in the Data URI scheme is easy to read, easy to edit and small size.
>
>That Data URI scheme is written in for example, background-image, list-style-image, cursor CSS properties, src, data attributes of `<img>`, `<input>`, `<iframe>`, `<object>` elements, etc..
>
```
div {
  background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="96" height="96"><path d="M10,10L32,90L90,32z" fill="lightgreen"/></svg>') center no-repeat;
}
```
>But IE ignores it, and some browsers consider # as the hash.

Basically, IE follows the standards strictly. double quotes, hashses and arrows will therefore cause problems and the icons aren't displayed.

Hack: You *can* use this polyfill https://github.com/anseki/polyfill-svg-uri (`npm install --save polyfill-svg-uri`) and import it to your ./mypolyfill.ts `import 'polyfill-svg-uri/polyfill-svg-uri';`. **But** it can cause other problems in Chrome.

A better solution is to use the [svgo](https://github.com/ben-eb/postcss-svgo) plugin.

Run

`npm install postcss-svgo --save-dev`

and add svgo plugin to your `gulpfile.js`

```
var postcss = require('gulp-postcss');
var gulp = require('gulp');
var svgo = require('postcss-svgo');

gulp.task('svgfix', function () {
    var postcss = require('gulp-postcss');
    var processors = [
    svgo({encode: true})
    ];
    console.log('Gulp finished!')

    return gulp.src('./www/build/*.css')
        .pipe(postcss(processors))
        .pipe(gulp.dest('./www/build/'));
});

gulp.task('ionic:build:after', ['svgfix']);
```

### ion-title

Issue: Title text is not vertically centered

Cause: Nested flexbox elements fail to register the height of their parent element in IE

Hack: 

1. Add `min-height` property to .title .title-md

2. Wrap ion-title in a container. That is, add the following css to your app.scss

```
.FlexContainerWrapper {
  display: flex;
  flex-direction: column;
}

.FlexContainer {
  align-items: flex-start;
  //background: hsla(0,0%,0%,.1);
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin: 0;
  padding: 0;
  min-height: 50px;
  min-width: 0;
}

.FlexItem {
  //background: hsla(0,0%,0%,.1);
  box-sizing: border-box; /* 1 */
  max-width: 100%; /* 1 */
  padding: 0 1em;
}
```

And wrap any ion-title element like so:

```
<div class="FlexContainerWrapper">
  <div class="FlexContainer">
    <ion-title>
       Centered Title Text
    </ion-title>
  </div>
</div>
```
