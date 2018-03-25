---
title: Finding Mono-Spaced Fonts in JavaFX
date: 2015-07-27
tags:
  - java
  - javafx
categories: programming
---

There are many use cases where a mono-spaced (fixed-width) font is useful in programming. Programming editors and creating program listings come to mind. But there doesn't seem to be a consistent way of obtaining a list of all of the mono-spaced fonts installed in the operating system.

Back in the days of [Swing](https://en.wikipedia.org/wiki/Swing_%28Java%29), you usually had to grab a list of font families (e.g. Arial, Times New Roman, _etc_.) from [AWT](https://en.wikipedia.org/wiki/Abstract_Window_Toolkit) and then create a `BufferedImage` to print the font to and check layout widths. In [JavaFX](https://en.wikipedia.org/wiki/JavaFX), it seems a bit easier. Here's one way to do it.

```java
/**
* Return a list of all the mono-spaced fonts on the system.
*
* @return An observable list of all of the mono-spaced fonts on the system.
*/
private ObservableList<String> getMonoFontFamilyNames() {

    // Compare the layout widths of two strings. One string is composed
    // of "thin" characters, the other of "wide" characters. In mono-spaced
    // fonts the widths should be the same.

    final Text thinTxt = new Text("1 l"); // note the space
    final Text thikTxt = new Text("MWX");

    List<String> fontFamilyList = Font.getFamilies();
    List<String> monoFamilyList = new ArrayList<>();

    Font font;

    for (String fontFamilyName : fontFamilyList) {
        font = Font.font(fontFamilyName, FontWeight.NORMAL, FontPosture.REGULAR, 14.0d);
        thinTxt.setFont(font);
        thikTxt.setFont(font);
        if (thinTxt.getLayoutBounds().getWidth() == thikTxt.getLayoutBounds().getWidth()) {
            monoFamilyList.add(fontFamilyName);
        }
    }

    return FXCollections.observableArrayList(monoFamilyList);
}
```

It seems a little less complicated in that all of the needed functionality is available right in JavaFX. One thing that hasn't changed is that it can still be slow. For example, if you want to populate a `ComboBox` with all of the mono-spaced fonts at the start of program execution, it can add a small but noticeable delay.

In my own case, on Windows, I have about 370 fonts on the system. Twenty-seven of those tested as mono-spaced, including quite a few that I never use, mostly non-English character sets that get installed somehow.

Still, good to know.