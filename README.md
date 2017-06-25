# IonicOnIE
Hacks to make Ionic run on Internet Explorer 11

## Why run Ionic in IE?

Ionic is a html5 framework to create mobile apps for iOS, Android and Windows Universal Apps. It does not support IE.

Using Ionic to build is possible and if you stick to Chrome you will not have many technical problems. Beware of mobile-centric UI though.

Many users especially if you're dealing with enterprise clients, need to run their apps in the currently most despised browser: Internet Explorer.

This is a list of hacks that will make it possible.

### ion-item
Issue: ion-item collapses on IE. 

Reason:

Hack:

```
.md {
    ion-item{
        min-height: 8rem;
    }
}


### ion-datetime

Issue: Day and month list collapses into one

Reason:

Hack
