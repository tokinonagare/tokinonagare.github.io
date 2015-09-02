---
layout: 	  blog
title:		  Android Cursor 和 Query 问题处理
subtitle:   Android Studio 学习笔记
categories: Andriod
tags: 		  Andriod_Studio SQLite
redirect_from:
  - /web/Android_Cursor_and_Query_problem_solve.html
  - /2015/09/02/Android_Cursor_and_Query_problem_solve/
---

* Demo: https://github.com/tokinonagare/AndroidStudioSQLiteSample

`/app/src/main/java/com/tokinonagare/sqlitesample/MyDBHandler.java`

## Cursor

```bash
//Move to the first row in your results
cursor.moveToFirst();

while(!cursor.isAfterLast()) {
    if(cursor.getString(cursor.getColumnIndex("productname"))!= null) {
        sqLiteDatabaseString += cursor.getString(cursor.getColumnIndex("productname"));
        sqLiteDatabaseString += "\n";
    }
}
```

在这一段涉及Cursor取数据的代码中, 会出现点击addButton时, app卡死的情况, 内存似乎一直在益处, 感觉缺少终止代码.

研究了一下Cursor的方法后使用了如下代码替换, 解决了问题

```bash
while (cursor.moveToNext()){
    if(cursor.getString(cursor.getColumnIndex("productname"))!= null) {
        sqLiteDatabaseString += cursor.getString(cursor.getColumnIndex("productname"));
        sqLiteDatabaseString += "\n";
    }
}

cursor.close();
```

## Query

```bash
  @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        String query = "CREATE TABLE " + TABLE_PRODUCTS + " ( " +
                COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +
                COLUMN_PRODUCTNAME + " TEXT " +
                ");";
        sqLiteDatabase.execSQL(query);
    }
```

`AUTOINCREMENT, ` 这个逗号是为了避免出现 `near`的 Error 问题添加的.