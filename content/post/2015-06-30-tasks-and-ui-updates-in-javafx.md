---
title: Tasks and UI Updates in JavaFX
date: 2015-06-30
tags:
  - concurrency
  - java
  - javafx
categories:
  - programming
---

Recently, I answered a [question](http://stackoverflow.com/questions/30980138/javafx-indeterminate-progress-bar-while-doing-a-process/30986188#30986188) on [StackOverflow](http://stackoverflow.com/) related to executing a long-running task in the background of a JavaFX application while updating the UI on the progress of that task. I have found myself coming back to that solution several times now.

<!--more-->

It wasn't a very good question by the [standards of the forum](http://stackoverflow.com/tour), with little research or explanation. In fact, it had been down-voted. But it piqued my interest because of a similar problem I had recently. In a program that I have been working on, at start up, there is a check for the presence of a database. If the database is not found, the program needs to write a "seed" version. I wanted this to happen before the main UI came up, but with a window showing progress on the task. I asked a [similar question](http://stackoverflow.com/questions/30917285/javafx-splash-screen-message-and-progress-not-updating) on StackOverflow. (Things weren't working for me because of an embarrassing little bit of stupidity.)

An [example](https://gist.github.com/jewelsea/2305098) by the amazing [JewelSea](http://stackoverflow.com/users/1155209/jewelsea) provided the hint I needed. Since that time I have found myself using similar solutions for all kinds of problems.

Here's the answer I provided.

```java
package Demo;

import javafx.application.Application;
import javafx.concurrent.Task;
import javafx.concurrent.WorkerStateEvent;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ProgressBar;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;
import javafx.stage.StageStyle;

public class BackgroundWorkerDemo extends Application {

    private final static String CNTR_LBL_STR = "Counter: ";
    private int counter;

    Label counterLabel;
    Button counterButton;
    Button taskButton;

    @Override
    public void start(Stage primaryStage) {
        counter = 0;
        counterLabel = new Label(CNTR_LBL_STR + counter);
        counterButton = new Button("Increment Counter");
        counterButton.setOnAction(new EventHandler<ActionEvent>() {
            @Override
            public void handle(ActionEvent e) {
                counter++;
                counterLabel.setText(CNTR_LBL_STR + counter);
            }
        });
        taskButton = new Button("Long Running Task");
        taskButton.setOnAction(new EventHandler<ActionEvent>() {
            @Override
            public void handle(ActionEvent e) {
               runTask();
            }
        });

        VBox mainPane = new VBox();
        mainPane.setPadding(new Insets(10));
        mainPane.setSpacing(5.0d);
        mainPane.getChildren().addAll(counterLabel, counterButton, taskButton);
        primaryStage.setScene(new Scene(mainPane));
        primaryStage.show();
    }

    private void runTask() {

        final double wndwWidth = 300.0d;
        Label updateLabel = new Label("Running tasks...");
        updateLabel.setPrefWidth(wndwWidth);
        ProgressBar progress = new ProgressBar();
        progress.setPrefWidth(wndwWidth);

        VBox updatePane = new VBox();
        updatePane.setPadding(new Insets(10));
        updatePane.setSpacing(5.0d);
        updatePane.getChildren().addAll(updateLabel, progress);

        Stage taskUpdateStage = new Stage(StageStyle.UTILITY);
        taskUpdateStage.setScene(new Scene(updatePane));
        taskUpdateStage.show();

        Task longTask = new Task<Void>() {
            @Override
            protected Void call() throws Exception {
                int max = 50;
                for (int i = 1; i <= max; i++) {
                    if (isCancelled()) {
                        break;
                    }
                    updateProgress(i, max);
                    updateMessage("Task part " + String.valueOf(i) + " complete");

                    Thread.sleep(100);
                }
                return null;
            }
        };

        longTask.setOnSucceeded(new EventHandler<WorkerStateEvent>() {
            @Override
            public void handle(WorkerStateEvent t) {
                taskUpdateStage.hide();
            }
        });
        progress.progressProperty().bind(longTask.progressProperty());
        updateLabel.textProperty().bind(longTask.messageProperty());

        taskUpdateStage.show();
        new Thread(longTask).start();
    }

    public static void main(String[] args) {
        launch(args);
    }

}
```

The program puts up a little window with a counter, a button to increment the counter, and a button to start a long-running background task. Once the background task is started, another window is displayed that shows the progress of the task. The window is hidden when the task is completed.

While the task is running, the user can increment the counter any number of times by pressing the button. The user also can start multiple versions of the long-running task. All-in-all, a very general solution.