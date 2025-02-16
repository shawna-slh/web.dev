---
layout: post
title: 'prefers-reduced-motion: Sometimes less movement is more'
subhead: |
  The prefers-reduced-motion media query detects whether the user has requested
  the operating system to minimize the amount of animation or motion it uses.
authors:
  - thomassteiner
description:
  The prefers-reduced-motion media query detects whether the user has requested that the system
  minimize the amount of animation or motion it uses. This is for users who either require or prefer
  minimized animations; for example people with vestibular disorders often desire animations to be
  kept to a minimum.
date: 2019-03-11
updated: 2022-09-07
tags:
  - blog # blog is a required tag for the article to show up in the blog.
  - media-queries
  - css
hero: image/admin/LI2vYKZwQ98w3MLtUF8V.jpg
alt: Time-lapse of woman in a train.
feedback:
  - api
---

Not everyone likes decorative animations or transitions, and some users outright experience motion
sickness when faced with parallax scrolling, zooming effects, and so on. The user preference media
query `prefers-reduced-motion` lets you design a motion-reduced variant of your site for users who
have expressed this preference.

{% BrowserCompat 'css.at-rules.media.prefers-reduced-motion' %}

## Too much motion in real life and on the web

The other day, I was ice skating with my kids. It was a lovely day, the sun was shining, and the ice
rink was crammed with people ⛸. The only issue with that: I don't cope with crowds well. With so
many moving targets, I fail to focus on anything, and end up lost and with a feeling of complete
visual overload, almost like staring at an anthill 🐜.

<figure>
  {% Img src="image/admin/JA5v1s8gSBk70eJBB8xW.jpg", alt="Throng of feet of ice skating people.", width="580", height="320" %}
  <figcaption>Visual overload in real life.</figcaption>
</figure>

Occasionally, the same can happen on the web: with flashing ads, fancy parallax effects, surprising
reveal animations, autoplaying videos, and so on, _the web sometimes can be quite overwhelming_…
Happily, unlike in real life, there is a solution to that. The CSS media query
`prefers-reduced-motion` lets developers create a variant of a page for users who, well, prefer
reduced motion. This can comprise anything from refraining from having autoplaying videos to
disabling certain purely decorative effects, to completely redesigning a page for certain users.

Before I dive into the feature, let's take one step back and think of what animations are used for
on the web. If you want, you can also skip the background information and
[jump right into the technical details](#working-with-the-media-query) below.

## Animation on the web

Animation is oftentimes used to provide _feedback_ to the user, for example, to let them know that
an action was received and is being processed. For example, on a shopping website, a product could
be animated to "fly" into a virtual shopping cart, depicted as an icon in the top-right corner of
the site.

Another use case involves using motion to
[hack user perception](https://medium.com/dev-channel/hacking-user-perception-to-make-your-websites-and-apps-feel-faster-922636b620e3)
by using a mixture of skeleton screens, contextual metadata, and low quality image previews to
occupy a lot of the user's time and make the whole experience _feel faster_. The idea is to give
context to the user of what's coming and meanwhile load in things as quickly as possible.

Finally, there are _decorative_ effects like animated gradients, parallax scrolling, background
videos, and several others. While many users enjoy such animations, some users dislike them because
they feel distracted or slowed down by them. In the worst case, users may even suffer from motion
sickness as if it were a real life experience, so for these users reducing animations is a _medical
necessity_.

## Motion-triggered vestibular spectrum disorder

Some users experience
[distraction or nausea from animated content](https://www.w3.org/WAI/WCAG21/Understanding/animation-from-interactions.html).
For example, scrolling animations can cause vestibular disorders when elements other than the main
element associated with the scrolling move around a lot. For example, parallax scrolling animations
can cause vestibular disorders because background elements move at a different rate than foreground
elements. Vestibular (inner ear) disorder reactions include dizziness, nausea, and migraine
headaches, and sometimes require bed rest to recover.

## Remove motion on operating systems

Many operating systems have had accessibility settings for specifying a preference for reduced
motion for a long time. The screenshots below show macOS Mojave's **Reduce motion** preference and
Android Pie's **Remove animations** preference. When checked, these preferences cause the operating
system to not use decorative effects like app launching animations. Applications themselves can and
should honor this setting, too, and remove all unnecessary animations.

<div class="switcher">
  <figure>
    {% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/KwuLNPefeDzUfR17EUtr.png", alt="A screenshot of the macOS settings screen with the 'Reduce motion' checkbox checked.", width="398", height="300" %}
  </figure>
  <figure>
    {% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/qed7yE6FKVQ5YXHn0TbJ.png", alt="A screenshot of the Android settings screen with the 'Remove animations' checkbox checked.", width="287", height="300" %}
  </figure>
</div>

## Remove motion on the web

[Media Queries Level&nbsp;5](https://drafts.csswg.org/mediaqueries-5/) brings the reduced motion
user preference to the web as well. Media queries allow authors to test and query values or features
of the user agent or display device independent of the document being rendered. The media query
[`prefers-reduced-motion`](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-motion) is used
to detect if the user has set an operating system preference to minimize the amount of animation or
motion it uses. It can take two possible values:

- `no-preference`: Indicates that the user has made no preference in the underlying operating
  system. This keyword value evaluates as `false` in the boolean context.
- `reduce`: Indicates that the user has set an operating system preference indicating that
  interfaces should minimize movement or animation, preferably to the point where all non-essential
  movement is removed.

## Working with the media query from CSS and JavaScript contexts

As with all media queries, `prefers-reduced-motion` can be checked from a CSS context and from a
JavaScript context.

To illustrate both, let's say I have an important sign-up button that I want the user to click. I
could define an attention-catching "vibrate" animation, but as a good web citizen I will only play
it for those users who are explicitly OK with animations, but not everyone else, which can be users
who have opted out of animations, or users on browsers that don't understand the media query.

```css
/*
  If the user has expressed their preference for
  reduced motion, then don't use animations on buttons.
*/
@media (prefers-reduced-motion: reduce) {
  button {
    animation: none;
  }
}

/*
  If the browser understands the media query and the user
  explicitly hasn't set a preference, then use animations on buttons.
*/
@media (prefers-reduced-motion: no-preference) {
  button {
    /* `vibrate` keyframes are defined elsewhere */
    animation: vibrate 0.3s linear infinite both;
  }
}
```

{% Aside %} If you have a lot of animation-related CSS, you can spare your opted-out users from
downloading it by outsourcing all animation-related CSS into a separate stylesheet that you only
load conditionally via the `media` attribute on the `link` element&nbsp;😎:
`<link rel="stylesheet" href="animations.css" media="(prefers-reduced-motion: no-preference)">`
{% endAside %}

To illustrate how to work with `prefers-reduced-motion` with JavaScript, let's imagine I have
defined a complex animation with the
[Web Animations API](https://developer.mozilla.org/docs/Web/API/Web_Animations_API). While CSS rules
will be dynamically triggered by the browser when the user preference changes, for JavaScript
animations I have to listen for changes myself, and then manually stop my potentially in-flight
animations (or restart them if the user lets me):

```js
const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)');
mediaQuery.addEventListener('change', () => {
  console.log(mediaQuery.media, mediaQuery.matches);
  // Stop JavaScript-based animations.
});
```

Note that the parentheses around the actual media query are obligatory:

{% Compare 'worse' %}

```js
window.matchMedia('prefers-reduced-motion: reduce');
```

{% endCompare %}

{% Compare 'better' %}

```js
window.matchMedia('(prefers-reduced-motion: reduce)');
```

{% endCompare %}

## Working with the media query from `<picture>` contexts

An interesting use case is to make playing of an animated AVIF, WebP, or GIF dependent on the
`media` attribute. If `(prefers-reduced-motion: no-preference)` evaluates to `true`, it's safe to
display the animated version, else the static version:

```html
<picture>
  <!-- Animated versions. -->
  <source
    srcset="nyancat.avifs"
    type="image/avif"
    media="(prefers-reduced-motion: no-preference)"
  />
  <source
    srcset="nyancat.gif"
    type="image/gif"
    media="(prefers-reduced-motion: no-preference)"
  />
  <!-- Static versions. -->
  <img src="nyancat.png" alt="Nyan cat" width="250" height="250" />
</picture>
```

You can see the example below. Try toggling your device's motion preferences to see the difference.

<picture>
  <source srcset="https://storage.googleapis.com/web-dev-uploads/file/8WbTDNrhLsU0El80frMBGE4eMCD3/ZhnJj0x2s7oQ10vydXDy.avifs" type="image/avif" media="(prefers-reduced-motion: no-preference)">
  <source srcset="https://web-dev.imgix.net/image/8WbTDNrhLsU0El80frMBGE4eMCD3/5K63Q5tcQ3vLWAJWQfCp.gif" type="image/gif" media="(prefers-reduced-motion: no-preference)">
  <img src="https://web-dev.imgix.net/image/8WbTDNrhLsU0El80frMBGE4eMCD3/ipZALS4nnkUlleymYJXe.png" alt="Nyan cat" width="250" height="250">
</picture>

## Demo

I have created a little demo based on Rogério Vicente's amazing
[🐈 HTTP status cats](https://http.cat/). First, take a moment to appreciate the joke, it's
hilarious and I'll wait. Now that you're back, let me introduce the
[demo](https://prefers-reduced-motion.glitch.me). When you scroll down, each HTTP status cat
alternatingly appears from either the right or the left side. It's a buttery smooth 60 FPS
animation, but as outlined above, some users may dislike it or even get motion sick by it, so the
demo is programmed to respect `prefers-reduced-motion`. This even works dynamically, so users can
change their preference on-the-fly, no reload required. If a user prefers reduced motion, the
non-necessary reveal animations are gone, and just the regular scrolling motion is left. The
screencast below shows the demo in action:

<figure>
  {% Video src="video/tcFciHGuF3MxnTr1y5ue01OGLBn2/zWs45QPPI9C8CjF813Zx.mp4", muted=true, playsinline=true, controls=true, poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/CQAw3Ee43Dcv0JOsm9fl.png" %}
  <figcaption>Video of the
    <a href="https://prefers-reduced-motion.glitch.me"><code>prefers-reduced-motion</code> demo</a>
    app
  </figcaption>
</figure>

## Conclusions

Respecting user preferences is key for modern websites, and browsers are exposing more and more
features to enable web developers to do so. Another launched example is
[`prefers-color-scheme`](https://drafts.csswg.org/mediaqueries-5/#prefers-color-scheme), which
detects if the user prefers a light or dark color scheme. You can read everything about
`prefers-color-scheme` in my article [Hello Darkness, My Old Friend](/prefers-color-scheme) 🌒.

The CSS Working Group is currently standardizing more
[user preference media queries](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) like
[`prefers-reduced-transparency`](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-transparency)
(detects if the user prefers reduced transparency),
[`prefers-contrast`](https://drafts.csswg.org/mediaqueries-5/#prefers-contrast) (detects if the user
has requested the system to increase or decrease the amount of contrast between adjacent colors),
and [`inverted-colors`](https://drafts.csswg.org/mediaqueries-5/#inverted) (detects if the user
prefers inverted colors).

## (Bonus) Forcing reduced motion on all websites

Not every site will use `prefers-reduced-motion`, or maybe not significantly enough for your taste.
If you, for whatever reason, want to stop motion on all websites, you actually can. One way to make
this happen is to inject a stylesheet with the following CSS into every web page you visit. There
are several
[browser extensions](https://chrome.google.com/webstore/search/user%20stylesheets?_category=extensions)
out there (use at your own risk!) that allow for this.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  ::before,
  ::after {
    animation-delay: -1ms !important;
    animation-duration: 1ms !important;
    animation-iteration-count: 1 !important;
    background-attachment: initial !important;
    scroll-behavior: auto !important;
    transition-duration: 0s !important;
    transition-delay: 0s !important;
  }
}
```

The way this works is that the CSS above overrides the durations of all animations and transitions
to such a short time that they are not noticeable anymore. As some websites depend on an animation
to be run in order to work correctly (maybe because a certain step depends on the firing of the
[`animationend` event](https://developer.mozilla.org/docs/Web/API/HTMLElement/animationend_event)),
the more radical `animation: none !important;` approach wouldn't work. Even the above hack is not
guaranteed to succeed on all websites (for example, it can't stop motion that was initiated via the
[Web Animations API](https://developer.mozilla.org/docs/Web/API/Web_Animations_API)), so be sure to
deactivate it when you notice breakage.

## Related Links

- Latest Editor's Draft of the
  [Media Queries Level&nbsp;5](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-motion)
  spec.
- `prefers-reduced-motion` on
  [Chrome Platform Status](https://www.chromestatus.com/feature/5597964353404928).
- `prefers-reduced-motion` [Chromium bug](http://crbug.com/722548).
- Blink
  [Intent to Implement posting](https://groups.google.com/a/chromium.org/forum/#!msg/blink-dev/NZ3c9d4ivA8/BIHFbOj6DAAJ).

## Acknowledgements

Massive shout-out to [Stephen McGruer](https://github.com/stephenmcgruer) who has implemented
`prefers-reduced-motion` in Chrome and—together with
[Rob Dodson](https://twitter.com/rob_dodson)—has also reviewed this article.
[Hero image](https://unsplash.com/photos/im7Tiw1OY7c) by Hannah Cauhepe on Unsplash.
