---
layout: default
title: "Lab 16: Notes App with SQLite"
---

Persistent Data on a Mobile Client
==================================

In this lab you will implement a local SQLite database on the mobile device to store the notes. The application will allow the user to add new notes, delete existing notes, and clear the entire notes list. 

Getting Started
---------------

Download [CS496\_Lab16.zip](CS496_Lab16.zip) and import it into your Eclipse workspace.

Create a SQLite Database
------------------------

We will first add code to create a database when the app is first launched. This is done in the **MySQLiteHelper** class which subclasses [SQLiteOpenHelper](http://developer.android.com/reference/android/database/sqlite/SQLiteOpenHelper.html). Note that fields have already been added which specify the name of the database, name of the table, and names of the columns in the table. These fields will be used in the database class when the various persistence operations are performed.

-    Add a string **DATABASE_CREATE** that will create a new table with the two columns **COLUMN_ID** (which is the autoincrementing id key) and **COLUMN_TEXT** (which will store the text of each note). 

		private static final String DATABASE_CREATE = 
			"create table " + TABLE_NOTES + "(" + 
			COLUMN_ID + " integer primary key autoincrement, " +
			COLUMN_TEXT + "text not null" + 
			");";

-    Add code to **onCreate()** to execute the SQLite statement **DATABASE_CREATE** on the **database** parameter using the **execSQL()** method.

-    Add code to **onCreate()** to insert an initial note from the **string.xml** resource **first_note**. Use the **insert()** database method. Hint: Be sure to use a **ContentValues** object. You can retrieve the string resource from **mContext** using the **getString()** method

		mContext.getString(edu.ycp.cs.cs496.cs496_lab16.R.string.first_note); 

Note that **onUpgrade()** for this application will simply delete the old table and recreate a new one (which may have an updated schema). It would probably be better to write code to attempt some form of data migration rather than simply deleting all the previous data in the database.

Add Database Operations
-----------------------

You may wish to examine the **IDatabase** interface for the database operation method signatures.

In the **NotesDataSource** class, complete the methods **getNotes()**, **addNote()**, **deleteNote()**, and **deleteAllNotes()**. Note: The utility method **cursorToNote()** has been provide to perform the ORM operation of converting a **Cursor** object to a **Note**, i.e. converts a database row to a Java object.

-   **getNotes()** - use the **query()** method on the **database** object to obtain a **Cursor** containing all the rows of the database. Hint: The name of the table can be obtained from **MySQLiteHelper.TABLE_NOTES** along with the local **allColumns** field to specify that all columns should be retrieved (with all the remaining arguments set to **null**). Then extract **Note** objects from the cursor and add them to the list using

		cursor.moveToFirst();
		while (!cursor.isAfterLast()) {
			Note note = cursorToNote(cursor);
			notes.add(note);
			cursor.moveToNext();
		}

	**BE SURE** to call the **close()** method on the cursor.

-   **addNote()** - use the **insert()** method on the **database** object to add the **note** parameter object to the database. You will want to create a **ContentValues** object and call the **put()** method. Hint: You can retrieve the name of the text column using **MySQLiteHelper.COLUMN_TEXT**. Be sure to set the id field returned from the database for the **note** object.

-   **deleteNote()** - use the **delete()** method on the **database** object to delete the **note** specified by the parameter. Since the last parameter to **delete()** is an *array* of **String**'s, simply create a local **String** array from the parameter. **BE SURE** to use a placeholder in the *whereClause* argument.

-   **deleteAllNotes()** - again use the **delete()** method on the **database** object, but simply specify that all records should be removed. Hint: Use a statement similar to the previous method but specify **\*** in the *whereClause* argument with **null** as the *whereArgs*.

Perform Database Operations
---------------------------

-   Add code in **setDefaultView()** to the **onClick()** handler for the **addButton** to call the **addNote()** method of the **datasource** object to add a new record to the database. Hint: The method takes a **Note** object which must be created from the UI widget values.

-   Add code in **displayNoteView()** to the **onClick()** handler for the **deleteButton** to call the **deleteAllNotes()** method of the **datasource** object to delete all existing notes. Note that the view will be refreshed after this operation is performed.

-   Add code in **displayNoteView()** to the **onItemLongClick()** handler for the **ListView** items to call the **deleteNote()** method of the **datasource** object to remove the note that is being clicked (note here we are using a *long* click to indicate a delete is to be performed). Hint: The local **notes** variable stores a list of all the notes in the adapter and the **position** parameter indicates which list item is selected.
Note that the view will be refreshed after this operation is performed.