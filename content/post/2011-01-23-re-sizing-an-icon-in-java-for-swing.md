---
title: Re-sizing an Icon in Java for Swing
tags:
  - java
  - swing
id: 14
comment: false
categories:
  - programming
date: 2011-01-23 01:41:21
modified: 2018-03-15T10:12:15.352797-04:00
---

Sometimes you have a resource like an icon or image that _almost_ works, but not quite. For example, there was [a question on StackOverflow](http://stackoverflow.com/questions/1946631/resizing-an-icon-to-correctly-fit-in-a-scrollpane-corner-button/1947371#1947371) about how to re-size an existing icon to fit in the smaller location at the lower right corner of a `JScrollPane`. The original poster was close. Here's the code I came up with that actually worked.

```java
import javax.swing.*;
import java.awt.*;

public class CornerButton extends JFrame
{
    public CornerButton()
    {
        JTextArea area = new JTextArea(20, 20);
        JScrollPane scroll = new JScrollPane(area);
        Icon icn = UIManager.getIcon(&quot;OptionPane.questionIcon&quot;);
        int neededWidth = scroll.getVerticalScrollBar().getPreferredSize().width;
        int neededHeight = scroll.getHorizontalScrollBar().getPreferredSize().height;
        Image img = ((ImageIcon) icn).getImage();
        ImageIcon icon = new ImageIcon(img.getScaledInstance(neededWidth, neededHeight, Image.SCALE_AREA_AVERAGING));
        JButton smallBtn = new JButton(icon);
        scroll.setCorner(ScrollPaneConstants.LOWER_RIGHT_CORNER, smallBtn);
        this.add(scroll);
    }

    public static void main(String[] args)
    {
        CornerButton mainFrame = new CornerButton();

        mainFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        mainFrame.setVisible(true);
        mainFrame.setSize(200,200);
    }
}
```

This produces the following result:

[![A Program Window with the Resized Icon](/static/img/2011-01-23-WindowWithResizedIcon.PNG "A Program Window with the Resized Icon")<br><small>A Program Window with the Resized Icon</small>](/static/img/2011-01-23-WindowWithResizedIcon.PNG)

[![A Program Window with the Resized Icon](https://github.com/clartaq/yo-dave/raw/master/images/2011-01-23-WindowWithResizedIcon.PNG "A Program Window with the Resized Icon")<br><small>A Program Window with the Resized Icon</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-01-23-WindowWithResizedIcon.PNG)

See that little green question mark in the corner? That is the standard Java Swing icon used in `JOptionPane`s shrunk down to the size needed. Here's a blow up.

[![A Blowup of the Resized Corner Icon](/static/img/2011-01-23-CloseUpOfResizedIcon.PNG "A Blowup of the Resized Corner Icon")<br><small>A Blowup of the Resized Icon</small>](/static/img/2011-01-23-CloseUpOfResizedIcon.PNG)

[![A Blowup of the Resized Corner Icon](https://github.com/clartaq/yo-dave/raw/master/images/2011-01-23-CloseUpOfResizedIcon.PNG "A Blowup of the Resized Corner Icon")<br><small>A Blowup of the Resized Icon</small>](https://github.com/clartaq/yo-dave/raw/master/images/2011-01-23-CloseUpOfResizedIcon.PNG)

Seems to work pretty well.
