---
title: Focus Behavior Change between JavaFX 2 and JavaFX 8 when Selecting Rows in a TableView
date: 2015-04-18
modified: 2018-03-27T10:12:47.264862-04:00
tags:
  - java
  - javafx
  - perfectionism
categories:
  - programming
---

For a little while now, I've been working on an application that manages a list of documents, providing multiple views that the user can edit.

The application looks something like this:

[![Image of the document manager main screen](/static/img/2015_04_18_Main_Screen_Capture.PNG "The document manager main screen")<br><small>The document manager main screen</small>](/static/img/2015_04_18_Main_Screen_Capture_Cropped.PNG)

The user selects the document they wish to view or edit by selecting it from the large `TableView` in the middle of the window. The area on the right provides controls to view and edit details. (The area on the left is for filtering the documents displayed in the central table.)

Based on some early advice, I had watchers on the focus property of the fields that could be edited. When a control lost focus, any changes were written to the database. The user didn't have to do anything to save their work. It just happened.

This worked with Java 7 and JavaFX 2. After the switch to Java 8 and JavaFX 8, things were not quite the same. If a user was making a change somewhere and then selected another document without moving to another editing view, the data was lost. The focus change notification did not arrive before the new document was selected in the table (repopulating the editing control before the data was saved.)

<!--more-->

I posted a [question](http://stackoverflow.com/questions/29661714/change-in-focus-behavior-during-tableview-selection-between-javafx-2-and-8) about this, along with a [SSCCE](http://sscce.org/) on [StackOverflow](http://stackoverflow.com/). The gist of the few answers I received was that I was doing the updates wrong. [Kleopatra](http://stackoverflow.com/users/203657/kleopatra) informed me that I was probably doing the update wrong since there is no guarantee as to the order of various events and notifications. I had already tried doing the update during the document selection process of the `TableView` as suggested by [eckig](http://stackoverflow.com/users/4219144/eckig), but doing that left the application vulnerable to lost data if the user did an edit and just closed the program.

In the end, I _did_ use a listener on the change of selection in the table and did an override of the `stop()` method of the `Application` class to catch needed updates before closing.

But the key was creating a new class `MonitoredSimpleStringProperty`, derived from `SimpleStringProperty`, that keeps track of whether it's contents have been altered. With that information, the application can decide if it even needs to update the database as the user selects a different document to view or edit.

Another, longer SSCCE illustrates how it works:

```java
package focusproblem;

import javafx.application.Application;
import static javafx.application.Application.launch;
import javafx.beans.InvalidationListener;
import javafx.beans.Observable;
import javafx.beans.property.SimpleBooleanProperty;
import javafx.beans.property.SimpleStringProperty;
import javafx.beans.value.ChangeListener;
import javafx.beans.value.ObservableValue;
import static javafx.collections.FXCollections.observableArrayList;
import javafx.collections.ObservableList;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextArea;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class FocusProblem extends Application {

    private TextArea notesArea;
    private TableView docTable;
    private ObservableList<Doc> docList;

    private ObservableList<Doc> initDocs() {
        docList = observableArrayList();
        docList.add(new Doc("Harper Lee", "To Kill a Mockbird",
                "Some notes on mockingbirds"));
        docList.add(new Doc("John Steinbeck", "Of Mice and Men",
                "Some notes about mice"));
        docList.add(new Doc("Lewis Carroll", "Jabberwock",
                "Some notes about jabberwocks"));
        return docList;
    }

    private Parent initGui(ObservableList<Doc> d) {
        notesArea = new TextArea();
        notesArea.setId("notesArea");
        notesArea.setPromptText("Add notes here");

        TableColumn<Doc, String> authorCol = new TableColumn<>("Author");
        authorCol.setCellValueFactory(new PropertyValueFactory<Doc, String>("author"));
        authorCol.setMinWidth(100.0d);
        TableColumn<Doc, String> titleCol = new TableColumn<>("Title");
        titleCol.setCellValueFactory(new PropertyValueFactory<Doc, String>("title"));
        titleCol.setMinWidth(250.0d);

        docTable = new TableView<>(d);
        docTable.setEditable(true);
        docTable.setPrefHeight(200.0d);
        docTable.getColumns().addAll(authorCol, titleCol);
        docTable.getSelectionModel().selectedItemProperty().addListener(new SelectionChangeListener());
        VBox vb = new VBox();
        vb.getChildren().addAll(docTable, notesArea);
        return vb;
    }

    void updateDoc(Doc d) {
        for (SimpleStringProperty ssp : d.getDirtyFieldList()) {
            System.out.println("Updating field: " + ssp.getName()
                    + " with " + ssp.getValue());
            d.markClean();
        }
    }

    @Override
    public void start(Stage primaryStage) {
        primaryStage.setTitle("Focus Problem");
        primaryStage.setScene(new Scene(initGui(initDocs())));
        primaryStage.show();
    }

    @Override
    public void stop() {
        for (Doc d : docList) {
            updateDoc(d);
        }
    }

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        launch(args);
    }

    public class SelectionChangeListener implements ChangeListener<Doc> {

        @Override
        public void changed(ObservableValue<? extends Doc> observable,
                Doc oldDoc, Doc newDoc) {
            System.out.println("Changing selected row");
            if (oldDoc != null) {
                notesArea.textProperty().unbindBidirectional(oldDoc.notesProperty());
                updateDoc(oldDoc);
            }
            if (newDoc != null) {
                notesArea.setText(newDoc.getNotes());
                newDoc.notesProperty().bindBidirectional(notesArea.textProperty());
            }
        }
    }

    public class Doc {

        private final MonitoredSimpleStringProperty author;
        private final MonitoredSimpleStringProperty title;
        private final MonitoredSimpleStringProperty notes;

        public Doc(String auth, String ttl, String nts) {
            author = new MonitoredSimpleStringProperty(this, "author", auth);
            title = new MonitoredSimpleStringProperty(this, "title", ttl);
            notes = new MonitoredSimpleStringProperty(this, "notes", nts);
        }

        public void setAuthor(String value) {
            author.set(value);
        }

        public String getAuthor() {
            return author.get();
        }

        public MonitoredSimpleStringProperty authorProperty() {
            return author;
        }

        public void setTitle(String value) {
            title.set(value);
        }

        public String getTitle() {
            return title.get();
        }

        public MonitoredSimpleStringProperty titleProperty() {
            return title;
        }

        public void setNotes(String value) {
            notes.set(value);
        }

        public String getNotes() {
            return notes.get();
        }

        public MonitoredSimpleStringProperty notesProperty() {
            return notes;
        }

        public boolean isDirty() {
            return (author.isDirty() || title.isDirty() || notes.isDirty());
        }

        public ObservableList<MonitoredSimpleStringProperty> getDirtyFieldList() {
            ObservableList<MonitoredSimpleStringProperty> dirtyList = observableArrayList();
            if (author.isDirty()) {
                dirtyList.add(author);
            }
            if (title.isDirty()) {
                dirtyList.add(title);
            }
            if (notes.isDirty()) {
                dirtyList.add(notes);
            }
            return dirtyList;
        }

        public void markClean() {
            author.setDirty(false);
            title.setDirty(false);
            notes.setDirty(false);
        }
    }

    public class MonitoredSimpleStringProperty extends SimpleStringProperty {

        SimpleBooleanProperty dirty;

        public MonitoredSimpleStringProperty(Object bean, String name, String initValue) {
            super(bean, name, initValue);
            dirty = new SimpleBooleanProperty(false);
            this.addListener(new InvalidationListener() {
                @Override
                public void invalidated(Observable observable) {
                    dirty.set(true);
                }
            });
        }

        public MonitoredSimpleStringProperty(Object bean, String name) {
            this(bean, name, "");
        }

        public MonitoredSimpleStringProperty(String initialValue) {
            this(null, "");
        }

        public MonitoredSimpleStringProperty() {
            this(null, "", "");
        }

        public boolean isDirty() {
            return dirty.get();
        }

        public void setDirty(boolean newValue) {
            dirty.set(newValue);
        }
    }
}
```

The actual program is a bit more complicated, but this approach solved my problem. Now on to other things.

## An Aside -- How this Came About ##

I started working on this program as a result of something else I was trying to do. I've been working on a book -- "Molecular Dynamics Simulation for Beginners". As part of writing that, I need to maintain a list of references to be included for the reader that wants to dig deeper into some areas. The book is being written in LaTeX because of the math involved. Traditionally, LaTeX documents are handled by a program called BibTeX or something similar. There are lots of reference managers. Many are free. Many are open-source. None of them did exactly what I want. So... down the rabbit hole.
