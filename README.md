package com.example.sindhujak.myfirstapp;

import android.app.Activity;
//import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends Activity {
    TextView textview;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final SQLiteDB dbObj;
        dbObj = new SQLiteDB(getApplicationContext());

        Button button = (Button) findViewById(R.id.button);

        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View arg0)
            {
                dbObj.PutLog("log 1");
                Readlogs(dbObj);
            }
        });
        //Readlogs(dbObj);

    }
    public void Readlogs(SQLiteDB dbObj){
        textview = (TextView)findViewById(R.id.textView);
        String strLog = dbObj.ReadLogs(getApplicationContext());

        textview.setText(strLog);

    }
    public void getMessage(String msg){
        /*dbObj.PutLog(msg);
        textview = (TextView)findViewById(R.id.textView);
        String strLog = dbObj.ReadLogs(getApplicationContext());

        textview.setText(strLog);*/

        textview.setText(msg);

    }
}

/*public class MainActivity extends Activity {

    TextView NewText;
    Button ChangeText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        NewText = (TextView)findViewById(R.id.textview);
        ChangeText = (Button)findViewById(R.id.button);

        ChangeText.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {

                //Set Text on button click via this function.

                NewText.setText(" Button clicked ");

            }
        });
    }
}*/
/*
public class MainActivity extends Activity {
    TextView textview;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final SQLiteDB dbObj;
        dbObj = new SQLiteDB(getApplicationContext());

        Button button = (Button) findViewById(R.id.button);

        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View arg0)
            {
                dbObj.PutLog("log 1");
                Readlogs(dbObj);
            }
        });

    }
    public void Readlogs(SQLiteDB dbObj){
        textview = (TextView)findViewById(R.id.textview);
        String strLog = dbObj.ReadLogs(getApplicationContext());

        textview.setText(strLog);

    }
}*/


package com.example.sindhujak.myfirstapp;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

import java.text.SimpleDateFormat;
import java.util.Date;

/*public class SQLiteDB extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_sqlite_db);
    }
}*/
public class SQLiteDB extends SQLiteOpenHelper
{
    Context sqliteDBContext;
    private static final String DATABASE_NAME = "BitwiseSecurity";
    private static final int DATABASE_VERSION = 1;


    private static final String TABLE_Logger_Status = "Logger";


    public SQLiteDB(Context context)
    {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
        sqliteDBContext = context;
    }

    @Override
    public void onCreate(SQLiteDatabase db)
    {
        try
        {
            String LOGGER_TABLE = sqliteDBContext.getString(R.string.CreateLoggerTable);
            db.execSQL(LOGGER_TABLE);

        }
        catch(Exception e)
        {
            PutLog("[@@DB]EX onCreate "+e.toString());
        }
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        if (newVersion > oldVersion) {
            db.execSQL("DROP TABLE IF EXISTS " + TABLE_Logger_Status);
            onCreate(db);
        }
    }

    private static boolean objClosed = true;

    public void PutLog(String log) {

        SQLiteDatabase dbPutLog =  null;

        try {
            if(objClosed)
            {
                dbPutLog = this.getWritableDatabase();
                objClosed = false;
            }

            ContentValues values = new ContentValues();
            SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
            Date date = new Date();
            SimpleDateFormat LogDateFormat = new SimpleDateFormat("yyyyMMdd");

            values.put("Log", log + "-" + dateFormat.format(date));
            values.put("LogDate", LogDateFormat.format(date));
            // Inserting Row
            dbPutLog.insert(TABLE_Logger_Status, null, values);
        } catch (Exception e) {

        }
        finally {
            if (dbPutLog != null && dbPutLog.isOpen()) {
                dbPutLog.close();
                objClosed = true;
            }
        }
    }



    public String ReadLogs(Context ctx)
    {
        Cursor cursor = null;
        String strLogs="";
        SQLiteDatabase db= null;
        try
        {
            String selectQuery = sqliteDBContext.getString(R.string.GetLog);
            db = this.getWritableDatabase();
            cursor = db.rawQuery(selectQuery, null);

            if (cursor.moveToFirst())
            {
                do
                {
                    strLogs += cursor.getString(0)+ "\n";
                }
                while (cursor.moveToNext());
            }
        }
        catch(Exception e)
        {
            PutLog("[@@DB]EX ReadLogs "+e.getMessage());
        }

        finally {
            if(cursor!=null) {
                cursor.close();
            }
            if(db!= null && db.isOpen()) {
                db.close();
            }
        }
        return strLogs;
    }
}
