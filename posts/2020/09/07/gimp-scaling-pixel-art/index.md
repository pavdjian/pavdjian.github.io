# Scaling Pixel Art with GIMP


## Problem
I was recently commissioned to create a watch face for an individual, part of
the request was to make use of some 8-bit pixel art the user provided.
In itself this sounded like an easy enough task so I went about designing the watch
face thinking it would be a case of dropping the graphics in place and calling it a day.

However, once I added the png I was asked to use I ended up with something similar
to this

{{< image src="/posts/images/2020/09/07/initial_image_size.png" >}}

{{< admonition type=note open=true >}}
Full disclosure, the image above is actually making use of a 16-bit sprite,
but it gives a close approximation of the issue I was facing.
{{< /admonition >}}

In short, I had to find a consistent way to scale the source pixel art images
to support the user's device (as well as any other models I may need to support).


## Solution
I typically use GIMP for my image manipulation needs and its ability to scale images
was my go to tool of choice (`Image -> Scale Image...`)

{{< image src="/posts/images/2020/09/07/scale_image.png" >}}

By default, the tool makes use of the `Cubic` interpolation filter, however by setting
this filter to `None` I found that I was able to scale pixel art pretty much indefinitely.
It also turned out this scaling method meant I didn't have to try and clean up
any interpolation artifacts.

Pay particular attention to the image properties in the following two screenshots.
I've had to zoom in to show that the filter has kept the sprite identical at both
sizes, the original `16x21` pixels and the scaled `1600x2100` pixels.

{{< image src="/posts/images/2020/09/07/magnified_image.png" >}}

{{< image src="/posts/images/2020/09/07/scaled_image.png" >}}

The "magic" behind this method of image scaling is that the filter copies the
colour of each pixel from its closest neighbour. Often the result is an image that
has aliasing or pixelation, though in my case (and pixel art in general) this
isn't a problem but I couldn't recommend it for anything that may require anti-aliasing.
Then again, [other interpolation methods](https://links.avdjian.com/interpolation-filters)
are available and are more suited to those use cases.

Wrapping things up, a `1600x2100` resolution image might be a bit much
for a watch that has a total screen resolution of `240x240`, so if I was to
scale my example image correctly I would end up with something like this

{{< image src="/posts/images/2020/09/07/scaled_watch_image.png" >}}

And with that, thanks for reading.

