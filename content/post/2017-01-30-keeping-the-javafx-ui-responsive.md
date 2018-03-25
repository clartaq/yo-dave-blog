---
title: Keeping the JavaFX UI Responsive
tags:
  - programming
  - java
  - javafx
date: 2017-01-30 16:45:26
---

It's common knowledge that the JavaFX user interface toolkit is single-threaded. When your JavaFX-based program is doing things that can take some time, you need to run those tasks on a separate thread(s) to keep the interface responsive.

Recently, I've been working on a program that can spend a lot of time reading and writing to the disk, but at the same time I want to retain the ability for the user to change views of the UI as the work proceeds. I also want to provide the opportunity for the user to cancel the background task at any time. I thought I would provide a couple of examples of how I did that in the program.

<!--"more"-->

The program basically translates the contents of one file into many others. You can think of the three phases as importing a file of unknown size, summarizing the contents (and doing real-time UI updates), and then writing out the translated files. To be specific, the program translates [Evernote](https://evernote.com/) notes and notebooks into [TiddlyWiki](http://tiddlywiki.com/) tiddler files.

In the first phase the program reads a file in the Evernote [ENEX](https://blog.evernote.com/tech/2013/08/08/evernote-export-format-enex/) format. These types of files can range in size from a few kilobytes to hundreds of megabytes. Until the file is parsed, the program has no notion of how much work has to be done. But it can take a significant amount of time. During that time I want the user to be able to cancel at any time, thus the need for a responsive UI with a cancel button. A JavaFX progress dialog and a background Task are just right.

Here's an example of the progress dialog class that I used.

```java
/**
* A dialog to provide some progress feedback as the notes file is parsed.
*/
private class ImportProgressDialog {

    private final Stage dlgStage;
    private final ProgressIndicator pi = new ProgressIndicator();
    private final Button cancelBtn = new Button(&quot;Cancel&quot;);

    public ImportProgressDialog() {
        dlgStage = new Stage(StageStyle.UTILITY);
        dlgStage.setResizable(false);
        dlgStage.initModality(Modality.NONE);
        dlgStage.setTitle("Parsing...");

        pi.setProgress(-1.0d);

        final VBox dlgVBox = new VBox();
        dlgVBox.setSpacing(15.0d);
        dlgVBox.setPadding(new Insets(11.0d, 50.0d, 15.0d, 50.0d));
        dlgVBox.getChildren().addAll(pi, cancelBtn);

        Scene scene = new Scene(dlgVBox);
        dlgStage.setScene(scene);
    }

    public void bindToTask(final Task<?> task) {
        cancelBtn.setOnAction((ActionEvent event) -> {
            enableGuiItems();
            task.cancel();
            dlgStage.close();
        });
        cancelBtn.disableProperty().bind(Bindings.not(task.runningProperty()));
        dlgStage.setOnCloseRequest((WindowEvent event) -> {
            enableGuiItems();
            task.cancel();
        });
    }

    public void show() {
        dlgStage.show();
    }

    public void close() {
        dlgStage.close();
    }
}
```

There are a few interesting things happening here. The dialog is just a window (`Stage`). Unlike most dialogs, this one is not modal (`dlgStage.initModality(Modality.NONE)`) since we want the user to be able to use parts of the interface while the dialog updates them on progress. And since we don't know how much work there is to do, we use a `ProgressIndicator` that just shows some spinning dots (`pi.setProgress(-1.0d)`). The rest of the construct just lays out the visual components of the dialog.

The `bindToTask` method lets the process dialog shut down the task if the user presses the cancel button or the close decoration on the dialog. (The `disableGuiItems()` and `enableGuiItems()` methods disable and enable some parts of the GUI that should not be active while the task is running, like buttons that start the import, _etc_.)

The `Task` that the program uses with this dialog is quite simple. Leaving out the Javadocs:

```java
private static class ParseNotesTask extends Task<Void> {

    private final EnexParser p;
    private final File f;

    public ParseNotesTask(EnexParser ep, File noteFile) {
        p = ep;
        f = noteFile;
    }

    @Override
    public Void call() throws InterruptedException, JDOMException,
                              IOException, EnexInvalidFileException {
        p.parse(f);
        return null;
    }
}
```

The constructor just saves some variables for later. The real work is done in the `parse()` method of the `EnexParser p` (not shown) that is invoked by the task's `call()` method.

These two classes are used in the main body of the program as follows:

```java
private void populateUI(final File noteFile) {

    disableGuiItems();
    tGui.clear();

    EnexParser ep = new EnexParser();
    ParseNotesTask parseNotesTask = new ParseNotesTask(ep, noteFile);
    ImportProgressDialog pd = new ImportProgressDialog();
    pd.bindToTask(parseNotesTask);

    // Make sure to close the progress dialog when done.
    parseNotesTask.setOnSucceeded(event -> {
        pd.close();
        tGui.populateTreeViewGui(noteFile.getName(), ep);
        enableGuiItems();
    });
    parseNotesTask.setOnFailed((WorkerStateEvent arg0) -> {
        pd.close();
        new ExceptionDialog("While parsing the notes...",
        parseNotesTask.getException()).show();
        enableGuiItems();
    });

    pd.show();
    Thread thread = new Thread(parseNotesTask);
    thread.start();
}
```

So, the method creates the `ImportProgressDialog` and `ParseNotesTask` and binds them together. It also sets up two event handlers. On successfully parsing the file, the dialog is closed and the next phase of the program is started with `tGui.populateTreeViewGui()`. If parsing fails, the progress dialog is still closed, but a new dialog reporting the cause of the exception is displayed. After all of the setup, the progress dialog is displayed and the file parsing task is started.

As mentioned above, once the file is parsed, the GUI can be updated with a summary of the results. In this case, a `TreeView` is created containing metadata about the ENEX file and nodes summarizing the content of each of the notes. There can be thousands of notes in a file, requiring significant additional computation. As the summary of each note becomes available, the GUI is updated.

So we have a similar yet distinct need here. We want to show progress, allow the user to cancel ongoing work on the summaries, and update the GUI with summary information as it becomes available. This work differs slightly in that we know at the outset how many notes must be summarized. The progress dialog can show how far along the process is as it proceeds.

Here is the progress dialog class for this phase of the program.

```java
    private static class ProgressDialog {

        private final Stage dlgStage;
        private final ProgressIndicator pi = new ProgressIndicator();
        private final Button cancelBtn = new Button("Cancel");

        public ProgressDialog() {
            dlgStage = new Stage(StageStyle.UTILITY);
            dlgStage.setResizable(false);
            dlgStage.initModality(Modality.NONE);
            dlgStage.setTitle("Summarizing...");

            pi.setProgress(-1.0d);

            final VBox dlgVBox = new VBox();
            dlgVBox.setSpacing(15.0d);
            dlgVBox.setPadding(new Insets(11.0d, 50.0d, 15.0d, 50.0d));
            dlgVBox.getChildren().addAll(pi, cancelBtn);

            Scene scene = new Scene(dlgVBox);
            dlgStage.setScene(scene);
        }

        public void bindToTask(final Task<?> task) {
            pi.progressProperty().bind(task.progressProperty());
            cancelBtn.setOnAction((ActionEvent event) -> {
                task.cancel();
                pi.progressProperty().unbind();
            });
            cancelBtn.disableProperty().bind(Bindings.not(task.runningProperty()));
            dlgStage.setOnCloseRequest((WindowEvent event) -> {
                task.cancel();
            });
        }

        public void show() {
            dlgStage.show();
        }

        public void close() {
            dlgStage.close();
        }
    }
```

This class is nearly identical to the progress dialog class above with the exception of one very important line in the `bindToTask()` method:

```java
    pi.progressProperty().bind(task.progressProperty());
```

This statement provides a line of communication between the `Task` that we bind to this dialog, allowing it to report back a more quantitative estimate of the work completed.

In this case, the `Task` is actually simpler since it does not require a constructor to pass in information required to operate. So, this time I've created the `Task` inline. Here's how that code looks:

```java
        int numNotes = p.getNoteCount();

        Task<Void> addNotesTask = new Task<Void>() {
            @Override
            public Void call() throws InterruptedException {
                for (int i = 0; i &lt; numNotes; i++) {
                    final int finalI = i;
                    TreeItem<String> tis = nextNote(finalI, p);
                    Platform.runLater(() -> rootItem.getChildren().add(tis));
                    updateProgress(i, numNotes);
                }
                updateProgress(numNotes, numNotes);
                return null;
            }
        };

        ProgressDialog pd = new ProgressDialog();
        pd.bindToTask(addNotesTask);

        addNotesTask.setOnSucceeded(event -> {
            pd.close();
        });

        pd.show();
        Thread thread = new Thread(addNotesTask);
        thread.start();
```

The calls to `updateProgress()` in the `addNotesTask` updates the number of notes completed. That information gets passed out to the `ProgressDialog`, which updates it's display.

The hard work within the Task is being done by the `nextNote()` method to create information for the GUI. Since the `addNotesTask` is updating the GUI, it has to do that back on the JavaFX GUI thread. That's why it's being called via `Platform.runLater()`. Another interesting note is the use of the `finalI` variable. Since variables used in a lambda expression must be final or effectively final, you can't just use `i`.

One final thing you might notice is that there is no handler for a failure of the `addNotesTask`. The methods called do not produce any checked exceptions other than the `InterruptedException`. In this case, I prefer not to check or respond.

## Conclusion

It's pretty easy to keep your JavaFX GUI responsive even while doing substantial computational work in the background. A combination of background `Task`s and progress dialogs allows a user to see how such long computations are proceeding while incrementally updating the GUI as the results become available.

This methodology works, but still feels a little rough. In particular, I have configuration of the dialog and task scattered about a bit. I probably need to do a little more noodling to make that a bit cleaner.
