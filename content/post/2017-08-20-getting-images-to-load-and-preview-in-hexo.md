---
title: Getting Images to Load and Preview in Hexo
date: 2017-08-20 13:55:37
modified: 2018-03-27T11:32:00.344604-04:00
tags:
  - blogging
  - markdown
  - hexo
---

As part of the work of transferring old blog posts from other systems (mostly [WordPress](https://wordpress.com/)) to [Hexo](https://hexo.io/), I hit a bump when trying to use images in the new posts.

There are basically two recommended ways to access images in Hexo.

1. Put a new sub-directory, say `images` in your Hexo source directory. Put your images in your new directory. In your posts, use the usual [Markdown method of linking to images](https://daringfireball.net/projects/markdown/syntax#img), that is something like `![Alt text for image](/images/MyImage.png)`. But that doesn't work for me, no matter what I try. Generating and deploying appears to copy the image files to the right place, but I can't seem to load them.
2. Use a "Resource" directory and some special templating in the post. (See the actual documentation [here](https://hexo.io/docs/asset-folders.html).) Going this route does not let me preview the images (and captions, *etc*.) as I type the post.

So, I finally got things working to my satisfaction by using a third method. The steps I describe are specific to hosting on [GitHub Pages](https://pages.github.com/), but something similar might work for a different hosting method too. It basically involves deploying the image to the GitHub repository, then grabbing the link from the repository to use in your post.

1. If you haven't already, go ahead and create an `images` sub-directory inside your `source` directory.
2. Copy an image that you want to appear in your post into the `images` sub-directory.
3. Run `hexo generate -d` to regenerate and deploy your blog. This will copy the image into the `public\images` directory in the blog site. It will also put the new image into the GitHub repository for the site.
4. Now, just write your post. When you want to link to the image, go to the GitHub repository for the site and find the image file in the repository.
5. Copy the link to the image. In my case, using Mozilla, I right-click on the image file name in the repository and select "Copy Link Location" to copy the URL to the clipboard.
6. Back in your Markdown for the post, create a new link and paste the URL into it. Something like this: `![](https://github.com/clartaq/yo-dave/blob/master/images/2017-08-20-HappyFaceWink.png)`.
7. We are almost there, but this won't quite work either. Notice where the link refers to `.../blob/...`? Change that to `.../raw/...`. That should do it.

In most of the Markdown editors that I have used, referencing the image with a link in this form will show the image in the preview of the Markdown. It will also show the image in the published post. Here's an example including some extra stuff to use as a hover message, caption, and link to load the image alone.

`[![Happy Face Winking](https://github.com/clartaq/yo-dave/raw/master/images/2017-08-20-HappyFaceWink.png "An Orange Happy Face Winking")<br><small>A Happy Face Winking</small>](https://github.com/clartaq/yo-dave/raw/master/images/HappyFaceWink.png)
`

[![Happy Face Winking](/static/img/2017-08-20-HappyFaceWink.png "An Orange Happy Face Winking")<br><small>A Happy Face Winking</small>](/static/img/2017-08-20-HappyFaceWink.png "An Orange Happy Face Winking")

**Update**: 25 March 2018. Since moving the blog to Hugo, the strategy has changed. the image above is actually displayed here using the following:

`[![Happy Face Winking](/static/img/2017-08-20-HappyFaceWink.png "An Orange Happy Face Winking")<br><small>A Happy Face Winking</small>](/static/img/2017-08-20-HappyFaceWink.png)`

