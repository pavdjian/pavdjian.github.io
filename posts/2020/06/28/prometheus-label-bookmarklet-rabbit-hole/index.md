# Prometheus Label Bookmarklet Rabbit Hole


Following on [from my previous post]({{< ref "prometheus-label-bookmarklet.md" >}}) where I showed the bookmarklet I
created to allow users to persist labels on Prometheus' targets page, I wanted to write a post explaining how I arrived
at the point of the bookmarklet.

{{< admonition type=warning open=true >}}
I'm not a frontend developer and haven't really written javascript in a good few years, the below is just meant to
showcase my approach to solving a problem rather than my prowess in making browsers do my bidding.
{{< /admonition >}}

Quick summary of the problem, I saw the "before" labels as a means of deciding what relabel configurations I wanted
to introduce into each scrape configuration I was creating, however the "before" labels were only visible during
a mouseover event which was frustrating me no end.

Digging into the source code to understand how the mouseover tooltip is generated, I noticed that the list of "before"
labels was not a dynamic page element, the content of the tooltip is embedded within the pages' HTML, so I knew I
just needed a way to get to the underlying HTML.

Initially this was achieved using the inspect element tool, this was ok if a bit clunky.

{{< image src="/posts/images/2020/06/28/before_labels.png" >}}

While inspecting the list of labels I noticed a snippet of code `data-toggle="tooltip"`, surely we could change `tooltip`
for something else.

Chucking that snippet into Google, the search results all pointed to something called Bootstrap.
I knew it was a javascript library, so I went over to the [Bootstrap documentation](https://getbootstrap.com/docs/4.1/components/tooltips/)
to find out more.

It just so happens that Bootstrap can provide a number of components to your page, I didn't really know what I was
after at the time so there was a bit of trial and error involved in finding the component with the behaviour that would
suit my use case, but eventually I came across Popovers and they seemed to do what I wanted.
The example code provided in the documentation also looked pretty similar to the tooltip code, so it felt like a pretty
good replacement.

Tooltips
```js
$(function () {
  $('[data-toggle="tooltip"]').tooltip()
})
```

Popovers
```js
$(function () {
  $('[data-toggle="popover"]').popover()
})
```

I assumed that since the tooltip functionality worked on labels, the jQuery selector for all `'[data-toggle="tooltip"]'` was working, but no harm in verifying

{{< image src="/posts/images/2020/06/28/jQuery_selector.png" >}}

Ok great, so can we change the `tooltip()` method for `popover()` method?

`$('[data-toggle="tooltip"]').popover()`

{{< image src="/posts/images/2020/06/28/jQuery_replacement.png" >}}

That seemed to work nicely and I was quite content to stop there initially.
However, as I started to make use of these popovers I quickly noticed that the default popover width wasn't large enough
and made reading label lists quite difficult as they spanned multiple lines a lot of the time.

{{< image src="/posts/images/2020/06/28/popover_size.png" >}}

I noticed that when the popover is created, a new `div` is inserted into the DOM with a class of
`class="popover fade bs-popover-right show"`, inspecting this element in the console I also noticed there was a
`max-width` attribute defaulting to `276px`. What if I were to change that value?

`$(".popover").css({"max-width":"700px"})`

{{< image src="/posts/images/2020/06/28/popover_class.png" >}}

Perfect, well almost...See the problem is, if that DOM element doesn't exist, the CSS change can't take effect.
In practice this meant every time I opened up a new popover, I'd have to re-run the CSS change.

This could probably suffice for personal usage, it didn't have to be perfect, but I've come this far and since
I saw some use in what I was doing I thought to take it a step further and create something for folk across the
organisation to use without having to copy and paste snippets every time they loaded the targets page.

First thing's first, I had to find all the labels on the page and add an [EventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
to them.

```
var elements = document.getElementsByClassName("badge badge-primary");

for (var i = 0; i < elements.length; i++) {
    elements[i].addEventListener('click', myFunction, false);
}
```

Next up was to package the commands I was running manually into a function which would be called each time the
`addEventListener` is triggered.

```
var elements = document.getElementsByClassName("badge badge-primary");

var myFunction = function(){
    $('[data-toggle="tooltip"]').popover();
    $(".popover").css({"max-width":"700px"};
};

for (var i = 0; i < elements.length; i++) {
    elements[i].addEventListener('click', myFunction, false);
}
```

For some reason, this didn't work as I expected, what I noticed was that on the initial click, the popover would stay at
the smaller `276px` size, and subsequent popovers would be sized correctly at `700px`.
Thinking through why this might be happening I remembered that the popover element is inserted into the DOM when created,
when `myFunction` was called on the initial click the jQuery library wasn't able to find any elements with a
`.popover` class.

To combat this I added a slight delay of 100ms before editing CSS attributes of the popover using the
[`setTimeout()` method](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout).

```
var elements = document.getElementsByClassName("badge badge-primary");

var myFunction = function(){
    $('[data-toggle="tooltip"]').popover();
    setTimeout(function(){$(".popover").css({"max-width":"700px"})}, 100);
};

for (var i = 0; i < elements.length; i++) {
    elements[i].addEventListener('click', myFunction, false);
}
```

At this point I had a small script that did what was needed, so it was time to package it into a bookmarklet so it could
be saved and used on the appropriate page. I can't remember what tool I used to achieve this, but a
[quick search](https://links.avdjian.com/bookmarlet-tools) provides more than enough tools which achieve the same result.

There probably is a bit of cleanup and best practice I should make sure I am following, maybe even add some defensive
code so that the bookmarklet would only run the script on designated pages, but since the current user count is 1...
it's probably a little much at this point in time 😄

And with that, thanks for reading.

