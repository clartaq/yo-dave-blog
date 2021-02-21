---
title: Overwhelming Java's Swing
date: 2015-01-06 09:47:27
tags:
  - java
  - swing
categories:
  - programming
---

An odd thing happened recently. I have been working on a simulation that produces graphical and text updates as it progresses. That involves drawing a picture of the system and updating some textual elements of the user interface.

Things seemed to work fine when the simulation was run with "normal" inputs. However, when supplied with a set of parameters to produce a much simpler simulation, the user interface essentially locked up.

<!--more-->

In my experience, this typically happens when the program tries to update the UI on the Swing Event Dispatch Thread (EDT) or when too much work is being done on the EDT. Well, the simpler simulation didn't seem to be doing more work than one of typical complexity. So, I got out my tools to investigate if I was misusing the EDT somehow. Those investigations turned up nothing.

I started up the profiler in NetBeans to see where the program was spending its time.  Nothing. Then I adjusted some profile settings to include classes beyond just those in my project. Bang. The program was spending most of its time trying to update a `JTextField`. It turned out that when running the simple simulation, the program was attempting to update a text field over 40,000 times per second. When the request rate decreased to about 30,000 per second or slower, things started working normally. The simulation of the simpler system was just overwhelming Swing's ability to update on my system. (Your mileage may vary.)

My first solution was to set a number of simulation iterations that had to be completed before calling for the UI update. That worked but was unsatisfying because more complicated simulations could take much longer to iterate. In those cases, I wanted the UI to be updated as quickly as possible, on every iteration.

What I really wanted was to update the UI as fast as possible up to a certain maximum rate. But what was that rate? How should it be determined?

Well, the answer for me is that is doesn't make much sense to update the UI any faster than my display can update. My monitor has a refresh rate of about 60 Hz. That should be the limit on my system. But what should it be on the other systems where my program was running. Turns out, it isn't too hard to figure out and implement, but it was something new to me.

In the main frame for my program (a `JFrame`), the constructor examines the graphics environment to determine the frame rate parameters from the monitor refresh rate.

```java
...
import java.awt.GraphicsDevice;
import java.awt.GraphicsEnvironment;
...
public class MainFrame extends javax.swing.JFrame;
...
public MainFrame()  {
        super("Frame Title");

        GraphicsEnvironment ge = GraphicsEnvironment.getLocalGraphicsEnvironment();
        GraphicsDevice[] gs = ge.getScreenDevices();

        if (gs.length > 1) {
            LOGGER.error("More than one graphics device.");
        }
        for (GraphicsDevice g : gs) {
            DisplayMode dm = g.getDisplayMode();
            int refreshRate = dm.getRefreshRate();
            if (refreshRate != DisplayMode.REFRESH_RATE_UNKNOWN) {
                frameNanos = 1000000000L/refreshRate;
            }
        }
    }
...
```

In the code above, the global variable `frameNanos` is calculated such that it corresponds to the number of nanoseconds between frame refreshes on the monitor. Later in the program, that value is used to throttle the number of calls to update the graphics.

```java
...
static long lastTime;
...
public void initGraphics() {
    ...
    lastTime = System.nanoTime();
    ...
}

public void doOutputUpdates(SimModel2D simSoFar) {
    if ((System.nanoTime() - lastTime) > frameNanos) {
        lastTime = System.nanoTime();
        ...
        displayPanel.updateDisplay(simSoFar);
    }
}
```

Now I have a method to update the user interface at a reasonable rate regardless of the speed of the underlying simulation. No more overwhelming Swing.
