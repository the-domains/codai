---
datePublished: '2017-03-09T03:06:08.565Z'
sourcePath: _posts/2015-11-21-saliency-extraction.md
inFeed: true
authors: []
hasPage: true
keywords: []
author: []
via: {}
dateModified: '2017-03-09T03:06:07.945Z'
title: The Grid’s Eyes
publisher: {}
description: >-
  We apply algorithms and techniques from the Computer Vision field. Combined
  with hand picked datasets we can create extractors for features we are
  interested in. With that our AI can answer questions like "what is a good
  color palette from this image?", "where is the best place to crop that
  picture?" or "can we place text on top of this image? and where?".
inLanguage: null
datePublishedOriginal: '2017-03-09T03:06:08.565Z'
starred: false
url: the-grids-eyes/index.html
_type: Article

---
We apply algorithms and techniques from the Computer Vision field. Combined with hand picked datasets we can create _extractors_ for features we are interested in. With that our AI can answer questions like _"what is a good color palette from this image?"_, _"where is the best place to crop that picture?"_ or _"can we place text on top of this image? and where?"_.

# The Grid's Eyes
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/b2ed61a18de752ff2baddf11e22e94951a444f58.jpg)

Since last year I'm working together with [Jon Nordby][0], [Henri Bergius][1] and [Gabriela Thumé][2] on an **image analytics pipeline** to [The Grid][3]. Basically, we are teaching our AI to identify patterns and extract relevant information from raw image data.

Here I describe some of these feature extractors and how they impact the design of content you share on The Grid.

When we look into a picture we can naturally determine what points get our attention first. A human face, a specific object or a region with higher contrast, those are both examples of _salient areas_ or _regions_. We can easily recognize those areas, but computers don't. So we have to teach them.

[The Grid][3] uses a [saliency detection algorithm][4] which analyzes a given image distinguishing foreground and background regions. In summary, it divides the original image by means of little segments. Those segments are analyzed and separated in foreground and background groups. Segments similar to a foreground group are grouped together. After many iterations we have all pixels grouped in their respective groups. If we mark each pixel with a value symbolizing its group, we have what we call a saliency map.

It's easier to understand with an example. Given the original image, we have the following saliency map.
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/980a941955a95543596e554f371808d87c5eef0f.png)

Higher gray levels indicate more salient areas. [The Grid][3]'s AI now knows the region in white is the most relevant part of that image.

With this new information, [The Grid][3]'s Design Systems can now crop the original image to show only those regions to fit on any screen size, for example. Or it can place text on top of images avoiding those salient regions.

# Color palette extraction

**Caliper** - how we call The Grid's image analytics pipeline - reduces all image colors into a really small set of _n_ colors. This set represents the most predominant colors of an image. The Grid's[Colorverse][5] can now use this **color palette** as input to find the right contrastant color to paint some content placed on top of the image, or to generate a similar color to paint the background around an image.

# Face detection

We use Machine Learning to detect if an image has human faces on it and where they are. Combined with the information we already have about salient regions, The Grid's Design Systems can avoid cropping heads off or placing content on top of faces.
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/cb5a91853b72a3c165f940a2b6695dbff24757e2.png)

Face detection is also important to detect mood on face expressions and to recognize people.

# Metadata extraction

Images captured by digital cameras or processed by photo editing softwares include metadata like EXIF tags into their files. **Caliper** extracts information like rotation angle and geolocation. It's important to pre-process images, rotating them back to the expected direction, for example.

Other high level information like color histograms are also extracted and are used by The Grid's Design Systems to calculate contrast and other metrics.

# The infrastructure

The counterpart of Caliper is [ImgFlo][6]. While Caliper does image analyses, ImgFlo is responsable to process every image on The Grid. In other words, ImgFlo applies filters to images and does all kind of transformations.

Both Caliper, ImgFlo and all The Grid backend infrastructure is powered by a dataflow implementation also developed by us: [NoFlo][7] and [Flowhub][8]. Using a dataflow paradigm on Caliper makes it really simple to understand the flow of data between image analisys operations. We can actually show what Caliper looks like because it's not code but a graph.
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/7820072cc815f2c09528b19ed4d897f9661f1bbe.png)

It's a common NoFlo graph: each _node_ is a component that implements some operation (like _Detect Faces_ or _Extract Saliency_). Those components are connected by _edges_ where data flow through. In this case we have image data flowing through the graph and each node is extracting the features we discussed before.

# The future

One of the important aspects of our pipeline is scaling. We are currently working on new feature extractors like for _text detection_. That will make it possible to avoid placing text on top of image's text. We are also improving our existing solution for _face detection_ and researching Deep Learning models for _image classification_ and _sentiment analysis_.

Besides image analysis, we are always researching and experimenting alternative ways to use data extracted by **Caliper**. For example, we can use extracted information from text, images or videos to synthesize new content. Using text as a _seed_ we can generate an infinite number of unique images combining different shapes, curves and colors.
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/4b5743d42253d3c8fd5872ddeef2e2fd533b3d18.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/66a690fd3b9dc360820ab891e8fdc79bc867220d.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/c11fd3895f89cd45a1424ad72c33bfc9ff4ae683.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/2ef70cfcbcea2d5794392a858940abcb394ee05d.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/a5b5c35f13d94f5aad7c7f0501aee76a7d6a60b8.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/21d515bca1dff409150f7224917aa48749463a4b.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/77d9844dd9788b96ac02ea9383dc293e1cb8accb.gif)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/e74ce60670ac3a973fac3c695d5f34a99ba42178.gif)

We can apply more complex algorithms like Delaunay Triangulation to obtain meshes for favbanners or backgrounds.
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/d3529f644f823882219eaa2528b639767a9e5826.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/c0863c574c5a391651ab446a0417e71a68aab062.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/35b42b3b22884bae6356253e387b12d375e9c74e.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/748f2ad17b4115cb4a60c40d15ad613e061b3697.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/f4165bb8e13d7563e75a821d9db8ecefecc52135.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/c8af78b3c908d8a83aa90254219a400cfd92ce19.png)
![](https://s3-us-west-2.amazonaws.com/the-grid-img/p/72451e3d082260d35eae7aa5dc156645e31df146.png)

[0]: http://jonnor.com/
[1]: http://bergie.today/
[2]: http://gabithu.me/
[3]: https://thegrid.io/
[4]: https://github.com/the-grid/gmr-saliency
[5]: https://www.youtube.com/watch?v=5eKzhw-RVD8
[6]: http://imgflo.org/
[7]: http://noflojs.org/
[8]: http://flowhub.io/