# Deepdata
This is a experiment
Android Database Connectivity using SQLite

Step 1: Create a java class named DBHelper.java for creating database and tables in SQLite

DBHelper.java
package com.example.lenovo.myapplicationytution;
import android.content.ContentValues;
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
public class DBHelper extends SQLiteOpenHelper 
{
public static String DB_NM="test.db";
    SQLiteDatabase db;
public static String TBL_NM="EMP";
public static String COL_1="_id";
public static String COL_2="name";
public static String COL_3="salary";
public DBHelper(Context context) {
super(context,DB_NM, null, 1);
    }
public void onCreate(SQLiteDatabase db) {
        db.execSQL("create table emp (_id integer primary key autoincrement, name text,salary text)");
    }

public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
public  boolean insertData(String  name,String  sal)
    {
db=this.getWritableDatabase();
        ContentValues cv=new ContentValues();
        cv.put(COL_2,name);
        cv.put(COL_3,sal);
long res=db.insert(TBL_NM, null, cv);
if (res==-1)
return false;
else
            return true;
    }
public  int updateData(String id,String  name,String  sal)
    {
        ContentValues cv = new ContentValues();
        cv.put(COL_2, name);
        cv.put(COL_3, sal);

// Call update method of SQLiteDatabase Class and close after
        // performing task
db = this.getWritableDatabase();
int res=db.update(TBL_NM, cv, "_id=?",
new String[]{id});
db.close();
return  res;
    }
}
Step 2: Design layout files of your activity as well as external layout shown as below:
Row.xml
(Note:this file is required for display data)

<RelativeLayout
    android:layout_height="match_parent"
    android:layout_width="match_parent"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/lblid"
        android:textSize="25dp"
        android:layout_alignParentTop="true"
        android:layout_alignParentStart="true" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/lblnm"
        android:textSize="15dp"
        android:layout_below="@+id/lblid"
        android:layout_alignEnd="@+id/lblid" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New Text"
        android:id="@+id/lblsal"
        android:textSize="25dp"
        android:layout_below="@+id/lblid"
        android:layout_alignParentEnd="true" />
</RelativeLayout>

activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:paddingBottom="@dimen/activity_vertical_margin"
android:paddingLeft="@dimen/activity_horizontal_margin"
android:paddingRight="@dimen/activity_horizontal_margin"
android:paddingTop="@dimen/activity_vertical_margin"
tools:context="com.example.dc.myapplicationconnectivity.MainActivity">

<TextView android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:textAppearance="?android:attr/textAppearanceLarge"
android:id="@+id/textView1"
android:text="Employee App"
android:layout_gravity="center"
android:layout_centerHorizontal="true"></TextView>

<EditText android:layout_width="match_parent"
android:layout_height="wrap_content"
android:id="@+id/txtEname"
android:hint="Enter Employee Name"
android:layout_below="@+id/textView1"
android:layout_alignParentEnd="true">
</EditText>

<EditText android:layout_width="match_parent"
android:layout_height="wrap_content"
android:id="@+id/txtSalary"
android:hint="Enter Salary"
android:layout_alignParentEnd="true"
android:layout_below="@+id/txtEname"></EditText>

<ListView android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:id="@+id/lvEmployees"
android:layout_below="@+id/btnAdd"
ndroid:layout_alignParentEnd="true"></ListView>

<Button android:id="@+id/btnAdd"
android:text="Add"
android:layout_height="wrap_content"
android:layout_width="wrap_content"
android:layout_below="@+id/txtSalary"
android:layout_alignParentStart="true"></Button>


<Button android:layout_height="wrap_content"
android:layout_width="wrap_content"
android:id="@+id/btnDelete"
android:text="Delete"
android:layout_above="@+id/lvEmployees"
android:layout_alignParentEnd="true"></Button>

<Button
android:id="@+id/btnUpdate"
android:text="Update"
android:layout_height="wrap_content"
android:layout_width="wrap_content"
android:layout_below="@+id/txtSalary"
android:layout_alignStart="@+id/textView1"
android:layout_marginStart="23dp" />
</RelativeLayout>

Step 3:Initialize necessary objects and call necessary methods to perform CRUD operation shown as below:

MainActivity.java

package com.example.lenovo.myapplicationytution;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.*;
public class MainActivity extends AppCompatActivity implements Button.OnClickListener{

    DBHelper Helper;
    SQLiteDatabase db;
// Adapter Object
SimpleCursorAdapter adapter;

static String eid;
    EditText edtnm, edtsal;
    Button add, upd, del;
    ListView l;

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
//object initialization
Helper = new DBHelper(this);

edtnm = (EditText) findViewById(R.id.txtEname);
edtsal = (EditText) findViewById(R.id.txtSalary);

add = (Button) findViewById(R.id.btnAdd);
upd = (Button) findViewById(R.id.btnUpdate);
del = (Button) findViewById(R.id.btnDelete);

l = (ListView) findViewById(R.id.lvEmployees);
        fetchData();

add.setOnClickListener(this);
del.setOnClickListener(this);
upd.setOnClickListener(this);

l.setOnItemClickListener(new AdapterView.OnItemClickListener() {
@Override
public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                Cursor c= (Cursor) parent.getItemAtPosition(position);
eid=c.getString(0).toString();
edtnm.setText(c.getString(1).toString());
edtsal.setText(c.getString(2).toString());
                Toast.makeText(getApplicationContext(), eid, Toast.LENGTH_LONG).show();
            }
        });
    }
// Fetch data from database and display into listview
private void fetchData() {
db = Helper.getReadableDatabase();
        Cursor c = db.rawQuery("SELECT * FROM "+Helper.TBL_NM, null);
adapter = new SimpleCursorAdapter(this, R.layout.row, c,
new String[]{DBHelper.COL_1, DBHelper.COL_2,
                            DBHelper.COL_3},
new int[]{R.id.lblid, R.id.lblNm, R.id.lblSal});
l.setAdapter(adapter);
        }
@Override
public void onClick(View v) {
if (v==add) {
if (edtnm.getText() == null || edtsal.getText() == null) {
                Toast.makeText(getApplicationContext(), "Data cannot be empty", Toast.LENGTH_LONG).show();

            } else {
                Boolean ins = Helper.insertData(edtnm.getText().toString(), edtsal.getText().toString());
if (ins) {
        Toast.makeText(getApplicationContext(), "Inserted", Toast.LENGTH_LONG).show();
                } else
Toast.makeText(getApplicationContext(), "Not inserted", Toast.LENGTH_LONG).show();
edtnm.setText(null);
edtsal.setText(null);
                fetchData();
            }
        }
if (v==del)
            {
db=Helper.getWritableDatabase();
int res=db.delete(Helper.TBL_NM,Helper.COL_1+"=?",new String[]{( eid)});
                 Toast.makeText(getApplicationContext(), res+" rows deleted", Toast.LENGTH_LONG).show();
edtnm.setText(null);
edtsal.setText(null);
                fetchData();
            }
if (v==upd){
int res=Helper.updateData(eid,edtnm.getText().toString(),edtsal.getText().toString());
                Toast.makeText(getApplicationContext(), res+" rows updated", Toast.LENGTH_LONG).show();
edtnm.setText(null);
edtsal.setText(null);
                fetchData();
            }
        }
    }


//py
from tkinter import *
x = tk()
//code
x.mainloop()
