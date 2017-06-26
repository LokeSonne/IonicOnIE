# IonicOnIE
Hacks to make Ionic run on Internet Explorer 11

## Why run Ionic in IE?

Ionic is a html5 framework for creating mobile apps for iOS, Android and Windows Universal Apps. It does not support IE.

Using Ionic to build desktop web apps is possible, and if you stick to Chrome you will not have many issues. Beware of mobile-centric componets though.

If your users need to run their apps in the currently most despised browser,  Internet Explorer, here is a list of hacks that will make it possible.

The list is not in anyway complete. Feel free to add to it.

## CSS3 and IE

So, a lot of features of CSS3 are not supported in Internet Explorer 11. Keywords like inherit, unset and intial will be ignored. That might cause trouble for your Ionic app, since it relies heavily on that.

I have yet to find a magic solution for this. using [Postcss](http://postcss.org) with some plugin, perhaps [oldie](https://github.com/jonathantneal/oldie) might do the trick, but I haven't made it work. So for CSS3 issues I've decided to go the tedious way and hack my way through it, one unaligned element at a time.


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
