---
layout: default
title: "Lecture 7: Mobile UI - Part I: Android Widgets"
---

View Heirarchy
==============

[Views](http://developer.android.com/reference/android/view/View.html) in a mobile application are typically associated with an *activity* and are used to render the UI associated with the activity. The view then contains components, known as *widgets*, for each UI element in the view. These widgets are then organized in the view using a *layout* that describes how the widgets are positioned. Finally, the appearance of the layout and widgets can be controlled/adjusted using *attributes* corresponding to the particular type of object.

Views can be created in several ways:

-   XML file - allows for the layout and widgets for a *static* view to be specified at build time.
-   Dynamic - allows for layouts and widgets to be added to a view at run time.
-   Combo - allows for a layout and some widgets to be specified statically and then additional ones added at run time.

Furthermore, multiple views can be combined together into a [ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html) object.

Once a view has been created (either statically or dynamically) an activity can select the current view to display using the method [setContentView()](http://developer.android.com/reference/android/app/Activity.html#setContentView(int\))).

Widgets
=======

The basic UI components used in a view are known as *widgets* (which are actually subclasses of View). Each widget will have numerous *attributes* which are used to control the appearance of the widget in the view. Once a widget is created, often they will have *event handlers* registered to them such that the application will perform various actions depending on how the user interacted with the widget. Usually each widget will be given a unique *id* attribute so that they can be accessed by the application. Additionally, widgets will usually specify their *layout\_height* and *layout\_width* attributes to describe how they will scale relative to the content they contain. Two typically values for these attributes are

> -   **wrap\_content** - the widget will dynamically size itself to fit the content within the widget
> -   **fill\_parent** - the widget will expand to the full extent of the parent view

Some common widgets are Button, TextView, EditText, CheckBox, RadioButton, and Spinner.

Button
------

One of the most common UI widgets is [Button](http://developer.android.com/reference/android/widget/Button.html) which represents a push-button type object. Usually they simply have some associated text which appears inside the button boundaries and have a click callback registered to them. Typically buttons will use *wrap\_content* attributes so they are rendered the size of the enclosed text. Hence the XML for a button might be:

    <Button
        android:id="@+id/dumbButton"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:text="@string/button_text"
    />

Then within the activity code, the click callback would be registered by calling the *setOnClickListener()* method on the Button object passing as a parameter a new *View.OnClickListener* object which has an overridden *onClick()* method. Thus an example would be

    final Button button = (Button) findViewById(R.id.dumbButton);
    button.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            // Whatever TODO when button is clicked
        }
    });

Note that the parameter to the onClick() method is a reference to the widget that was clicked.

Alternatively, the *onClick* attribute can be used to specify the name of the callback to be called when the button is clicked.

The background of a button can be set using the [background](http://developer.android.com/reference/android/R.attr.html#background) attribute which can either be a resource or a color value, e.g. android:background="\#FF0000" for a red button.

TextView
--------

[TextView](http://developer.android.com/reference/android/widget/TextView.html) widgets are typically used simply to place a label, i.e. static text, within a view. The text can be modified, however, if the TextView is given an *id* attribute and then the *setText()* method is called on a TextView object created from the resource *id*. Like all other widgets, any event handler can be registerd to a TextView if the application calls for user interaction with the widget. The extent attributes typically are specified as *wrap\_content* for the height\* and then either *wrap\_content* or *fill\_parent* for the width. An example XML for a TextView might be:

    <TextView
        android:id="@+id/nameLabel"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:text="@string/name_label"
    />

EditText
--------

For text boxes that are meant to be editable, i.e. the user can enter text in them, usually an [EditText](http://developer.android.com/reference/android/widget/EditText.html) widget is used. The text attribute (either in the XML or via the *setText()* method) can be used to prepopulate the text box with a default value if desired. Usually event handlers are not registered to EditText widgets, but rather the contents are retrieved from within the event handler of other widgets, e.g. when a button is pressed, using the *getText()* method on an EditText object. Thus typically the extents are set to *wrap\_content* for the height and *fill\_parent* for the width (so the EditText box fills the width of the screen). Example XML for an EditText widget might be

    <EditText
        android:id="@+id/name"
        android:layout_height="wrap_content"
        android:layout_width="fill_parent"
        android:text="@string/name_edit"
    />

CheckBox
--------

If there are certain choices that the user can select, often a [CheckBox](http://developer.android.com/reference/android/widget/CheckBox.html) widget is used. This widget displays a small box that can toggle checked or unchecked states. The *checked* attribute specifies the initial state of the CheckBox and the *text* attribute gives the text for the CheckBox. Typically the extents for CheckBoxes are *wrap\_content* in both dimensions. Event handlers can be registered for the widget if some behavior should be performed whenever the state changes, otherwise the *isChecked()* method can be called on a CheckBox object within another method to determine the current state of the CheckBox. While the CheckBox widget will keep track of its current state, the state of the CheckBox can also be changed programmatically using the *setChecked()* method if desired (for example when restoring a view). For example, XML for a CheckBox might be:

    <CheckBox
        android:id="@+id/capCheck"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/cap_check_text"
        android:onClick="onCapsClicked"
    />

Then in the activity code, the *onCapsClicked()* method might be defined as

    public void onCapsClicked(View v) {
        // Check if box is set or not
        if (((CheckBox) v).isChecked()) {
            // Do something when checked
        } else {
            // Do something else when unchecked
        }
    }

Note that the View parameter will be a reference to the CheckBox that was selected and thus is cast within the method.

RadioButton
-----------

Radio buttons are typically used for *mutually exclusive* choices the user can make. Thus they are grouped inside a [RadioGroup](http://developer.android.com/reference/android/widget/RadioGroup.html) object which defines which buttons can only have a single one selected. Thus when one [RadioButton](http://developer.android.com/reference/android/widget/RadioButton.html) is selected inside the group, all others are unselected. Usually all RadioButtons within a group will be assigned the same click handler (via the *onClick* attribute). Again usually *wrap\_content* attributes are specified and the *text* attribute is used for the label of each button. For example, XML for a RadioButton group might be:

    <RadioGroup
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">
        <RadioButton
            android:id="@+id/firstNameCheck"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/first_name_radio"
            android:onClick="onNameGroupClicked"
        />
        <RadioButton
            android:id="@+id/lastNameCheck"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/last_name_radio"
            android:onClick="onNameGroupClicked"
        />
    </RadioGroup>

RadioButton groups initially have none of the buttons selected (but will subsequently will always only have one selected unless the *clearCheck()* method is called on the RadioGroup to clear all selections). An initial selection can be set with the *setChecked()* method called for the desired option. The callback method in the Activity might then be:

    public void onNameGroupClicked(View v) {
        // Get radio button selected
        RadioButton rb = (RadioButton) v;
        // Perform appropriate action for selected option
    }

An alternative to RadioButtons when there are only two selectable options is to use a [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html) where the text for the two states can be set in the *textOn* and *textOff* attributes.

Spinner
-------

One other common widget for selectable lists of options is a [Spinner](http://developer.android.com/reference/android/widget/Spinner.html) which displays the list in a "slot machine" type control that allows the user to then choose one of the items in the list. The Spinner is populated with data from an [Adapter](http://developer.android.com/reference/android/widget/Adapter.html) (or one of its subclasses). Additionally, the prompt displayed inside the spinner can be specified in the *prompt* attribute for the widget. Thus example XML code for a Spinner might be:

    <Spinner
        android:id="@+id/titleSpinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/spin_title"
    />

To populate the items in the Spinner, one option is to use a string array (via the \<string-array\>\</string-array\> tags) stored in the **string.xml** resource file by creating XML with each item in \<item\>\</item\> tags. For example,

    <string-array name="titles">
        <item>Mr.</item>
        <item>Mrs.</item>
        <item>Miss</item>
    </string-array>

Then we can create an [ArrayAdapter](http://developer.android.com/reference/android/widget/Adapter.html) object from the string array stored in the resource and initialize the Spinner with the ArrayAdapter as:

    Spinner titleSpinner = (Spinner) findViewById(R.id.titleSpinner);
    ArrayAdapter<CharSequence> titleAdapter = ArrayAdapter.createFromResource(
        this, R.array.titles, android.R.layout.simple_spinner_item);
    titleAdapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
    titleSpinner.setAdapter(titleAdapter);
    titleSpinner.setOnItemSelectedListener(new TitleSelectedListener());

The two parameters *android.R.layout.simple\_spinner\_item* and *android.R.layout.simple\_spinner\_item* are Android build in layouts for standard spinner appearance and behavior. The behavior to perform when an item is selected from the Spinner is used is set using *setOnItemSelectedListener()* explained below.

Once the spinner is created, we associate a callback to the Adapter to set appropriate behavior when an item is selected from the Spinner. This is accomplished by creating a class that implements *OnItemSelectedListener* with two required methods - *onItemSelected()* and *onNothingSelected()*. Thus an example of a callback for the previous adapter might be:

    public class TitleSelectedListener implements OnItemSelectedListener {
        // Item selected
        public void onItemSelected(AdapterView<?> parent, View v, int pos, long id) {
            // Do something with selected object using
            // parent.getItemAtPosition(pos) to retrieve selected object
        }

        // Nothing selected
        public void onNothingSelected(AdapterView parent) {
            // Do whatever necessary if no item selected
        }
    }
