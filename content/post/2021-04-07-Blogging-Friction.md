---
author: david

title: Blogging Friction

date: 2021-04-07T12:55:22.334-04:00
modified: 2021-04-07T16:46:12.038-04:00
tags:
  - blogging

  - cwiki

  - friction

---

Let's face it. I'm an infrequent blogger. But I'm not an infrequent writer. So why does the (vast) majority of what I write never make it onto the blog?

The answer is **friction**. And why is that? Here is my process.
1. Think of something that I want to write down so I remember it.
2. Realize that what I've written might be useful to someone else too. (It could happen!)
3. Look at what I have to do in order to get my thoughts from my editor to the blog.
4. Feel much more interest in continuing on with whatever I'm working on.
5. Go do that other, interesting thing instead.

Since I use writing tools that I've developed myself, I have many fewer excuses than most people. But it doesn't get done.

After years of experimenting with a bazillion different text editors, the one I use is the editor I built into my personal wiki, [cwiki](https://github.com/clartaq/cwiki).

My editor is half-baked and I wouldn't want to inflict it on anyone else, but it does almost everything I want/need.

But it needs a "publish" or "blogify" button. There are a very few steps that I would need to take to prepare the finished wiki page for my blog.

The wiki can export to Markdown, the format needed by my blogging software, [Hugo](https://gohugo.io).

What else needs to happen?

1. Change the file name. Hugo requires that files be named just so in order to maintain its indices. It has to be stuck in a particular directory after reading the date embedded in the file name in a particular format.
2. Figures and Images. The requirements for this seem to change fairly frequently. This is not Hugos' fault. My blog site is on [GitHub Pages](https://pages.github.com). The rules for where graphics should be are very unclear, at least to me. I somehow manage to get things set up, but the requirements seem to change every so often.
3. Front Matter. What needs to be there? What is optional? What are acceptable formats? It used to be YAML, a terrible language for such information. Now it can be TOML too, much better for static information. I once made a half-hearted attempt to convert, but so many things broke that I reverted to YAML and haven't tried again.
5. Themes. How much information does the blog theme require to operate correctly? Is it all in the blogging software configuration? Or does some of it reside in each blog? Do some themes require different bits of information?
4. General Instability. Don't get me wrong, updates are a good thing. Bug fixes and expanded capabilities are good. But... I originally set up my current blog such that it generated no errors or warnings. That is no longer the case. There is new stuff that I am supposed to do. And the warnings don't give much information about where to learn about these new things. It's additional non-productive  maintenance to do.

These are a few simple items that need attention. But it is enough to annoy me. It was correct once. I have more interesting things to do than fiddle with someones blog generator. I can't imagine how off-putting this would be for someone who just wants to get their thoughts "out there" on the open web.

That's enough whinging for today. Back to work on something interesting.
 