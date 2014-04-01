---
layout: default
title: "Lecture 16: Android and SQLite"
---

SQLite
======

SQLite is a small footprint database management system that provides access via standard SQL commands, i.e. SELECT, INSERT, UPDATE, DELETE. It also supports transactions and is very robust in terms of database consistency. The main difference compared to a standard SQL system, e.g. mySQL, is that even though the columns in a SQLite database are given datatypes, they are not enforced. Thus, unlike a mySQL database, even if a column is defined to store INTEGER data, any type of data can actually be stored in that field. Thus it is the responsibility of the application code, i.e. ORM methods, to interpret the data appropriately.

Android API
===========

To utilize database functionality on the Android platform, we first subclass the **SQLiteOpenHelper** class which provides the interface to the database. This class requires that three methods be present:

-   *Constructor* - which calls the base class constructor.
-   **onCreate()** - which can be used to build an initial database, e.g. from string constants stored in **string.xml**
-   **onUpgrade()** - which can be used to modify a database based on a *version* number allowing migration from one schema to another (or possibly just deleting all previous tables and creating new ones)

Alternatively, rather than having the application create the database at run time, an SQLite database file can be included in the **assets** directory of the project (which subsequently will be embedded in the **.apk** file) and then simply *copied* onto the local database file.

Android provides two options for accessing data in a SQLite database

-   use the **query()**, **insert()**, **update()**, and **delete()** methods in the [SQLiteDatabase](http://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html) class to perform transactions on the database
-   use the **execSQL()** method to execute a single SQL statement against the database

Query()
-------

One common form of the **query()** method is

    Cursor query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy)

The query will return a Cursor\_ object (which is similar to a **ResultSet** in mySQL) and requires several parameters:

> -   **table** - the name of the table to run the query against
> -   **columns** - the list of columns to return the values of in the **Cursor**
> -   **selection** - filter for which rows to return, i.e. the WHERE clause without the WHERE keyword (set to **null** to return all rows)
> -   **selectionArgs** - a list of values to substitute for ? placeholders in the **selection**
> -   **groupBy** - filter corresponding to the GROUP BY SQL clause (**null** for no grouping)
> -   **having** - filter corresponding to the HAVING clause (**null** for no filter)
> -   **orderBy** - filter corresponding to the ORDER BY clause (**null** for default sort order)

For example, a **query()** for an item with name "Apples" might be

    Cursor c = db.query(INVENTORY_TABLE, null, "name=?", "Apples",null,null,null);

Insert()
--------

The form of the **insert()** method is

    long insert(String table, String nullColumnHack, ContentValues values)

The method returns the id of the inserted row (or -1 if an error occurs) has parameters:

> -   **table** - the name of the table to run the query against
> -   **nullColumnHack** - prevents an empty row from being inserted by specifying a column name to insert a **null** into (usually set to **null**)
> -   **values** - a **ContentValues** map that specifies the column name/value pairs to be inserted into the row.

For example, an **insert()** for an item with name "Oranges" and quantity 10 might be:

    ContentValues vals = new ContentValues();
    vals.put("name","Oranges");
    vals.put("quantity",10);
    long row = db.insert(INVENTORY_TABLE, null, vals);

Update()
--------

The form of the **update()** method is

    int update(String table, ContentValues values, String whereClause, String[] whereArgs)

The method returns an integer indicating the number of rows that were modified and has parameters:

> -   **table** - the name of the table to run the query against
> -   **values** - a **ContentValues** map that specifies column names/new values to be updated
> -   **whereClause** - filter to specify matching criteria to only update particular matching rows (**null** updates all rows)
> -   **whereArgs** - a list of values to substitute for any placeholders in the **whereClause**

For example, an **update()** to change the quantity of "Oranges" to 42 might be:

    ContentValues vals = new ContentValues();
    vals.put("quantity",42);
    int numRows = db.update(INVENTORY_TABLE, vals, "name=?", "Oranges");

Delete()
--------

The form of the **delete()** method is

    int delete(String table, String whereClause, String[] whereArgs)

The method returns an integer indicating the number of rows that were removed (if a whereClause is specified, 0 otherwise) and has parameters:

> -   **table** - the name of the table to run the query against
> -   **whereClause** - filter to specify matching criteria to only delete particular matching rows (**null** deletes all rows)
> -   **whereArgs** - a list of values to substitute for any placeholders in the **whereClause**

For example, a **delete()** to remove "Oranges" might be:

    int delRows = db.delete(INVENTORY_TABLE, "name=?", "Oranges");

ExecSQL()
---------

The form of the **execSQL()** method is

    void execSQL(String sql, Object[] bindArgs)

The method **cannot** return a value and thus cannot be used with SELECT statements. It has a single parameter:

> -   **sql** - which is a string representing the SQL statement to execute
> -   **bindArgs** - a list of values to substitute for any placeholders

For example, an alternative to the previous **delete()** call might be:

    String delSql = "DELETE FROM " + INVENTORY_TABLE + "WHERE name = ?";
    Object[] bindArgs = new Object[]{"Oranges"};
    db.execSQL(delSql, bindArgs);

Cursor
------

For SQLite queries that return data, e.g. SELECT statements, the results are provided in an object known as a [Cursor](http://developer.android.com/reference/android/database/Cursor.html) (sometimes subclassed to provide ORM functionality). **Cursor** objects have several commonly used methods:

> -   **getCount()** - returns the number of rows returned by the query
> -   **moveToFirst()** - reset the cursor to the first returned row
> -   **moveToPosition(int i)** - sets the cursor's position to the i<sup>th</sup> row
> -   **close()** - clean up the cursor resources
> -   **getColumnIndexOrThrow(String name)** - retrieves the column index for a given column **name**
> -   **getInt(int idx)**, **getString(int idx)**, etc. - gets the value of the field for column index **idx** as the corresponding datatype

Hence, if we assume that we have a **Cursor** returned from a query we could construct objects using:

    Cursor ic;

    // Obtain cursor via database query

    // Iterate over cursor rows
    for (int i = 0; i < ic.getCount(); i++) {
        // Get next row
        ic.moveToPosition(i);

        // Extract fields from row
        int id = ic.getInt(ic.getColumnIndexOrThrow("_id");
        String name = ic.getString(ic.getColumnIndexOrThrow("name");
        int quantity = ic.getInt(ic.getColumnIndexOrThrow("quantity");

        // Construct new model object
        Item item = new Item(id, name, quantity);
    }

    ic.close();
