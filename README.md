# NotePad-master 
#记事本应用--这是期中实验

# 期中实验
学号：123012016072 
姓名：陈琪
班级：软工2班

## 一、实验内容
笔记本应用：
基本要求：1、NoteList中显示条目增加时间戳显示
         2、添加笔记查询功能（根据标题查询）
		   
附加功能：根据自身实际情况进行扩充，以下是建议的扩展功能
				1、UI美化
				2、更改记事本的背景
				3、导出笔记，笔记的云备份和恢复
				4、添加代办功能
				5、笔记分类
				6、支持多种资源，如保存图片、语音、视频等（参考印象笔记）
				 7、语音搜索
				8、笔记便签

## 二、实验步骤和结果
## 目录结构：
**（1）关键代码**

**基本功能：**
1、NoteList中显示条目增加时间戳显示：
2、添加笔记查询功能（根据标题查询）：
 
**扩展功能：**
1、UI美化、更改记事本的背景：主要是对便签换了主题
2、导出笔记
3、对笔记进行排序：根据修改时间和创建时间排序
 4、对字体大小颜色进行改变
 
**（2）结果截图**

**基本功能：**
1、NoteList中显示条目增加时间戳显示：
 2、添加笔记查询功能（根据标题查询）：
 
**扩展功能：**
1、UI美化、更改记事本的背景：主要是对便签换了主题
2、导出笔记
3、对笔记进行排序：根据修改时间和创建时间排序
 4、对字体大小颜色进行改变
 
### (1)关键代码
### 基本功能：
#### 1、NoteList中显示条目增加时间戳显示：
**1、添加时间TextView：**
**noteslist_item.xml:**
```
<?xml version="1.0" encoding="utf-8"?>
<!--添加一个垂直的线性布局-->
<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
        <!--原标题TextView-->
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_vertical"
    android:paddingLeft="5dip"
    android:textColor="@color/black"
    android:singleLine="true" />
<!--添加显示时间的TextView-->
<TextView
    android:id="@+id/text2"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:textAppearance="?android:attr/textAppearanceSmall"
    android:paddingLeft="5dip"
    android:textColor="@color/black"/>
</LinearLayout>
```

**2、**
**（1）NotePadProvider.java中，创建的数据库中已经存在了COLUMN_NAME_CREATE_DATE，COLUMN_NAME_MODIFICATION_DATE，创建的时间和修改的时间都已经在数据库中。**
```
 @Override
       public void onCreate(SQLiteDatabase db) {
           db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
                   + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
                   + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
                   + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER"
                   + ");");
       }
```

**（2）NotesList的数据定义中增加修改时间**
```
 private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//在这里加入了修改时间的显示
    };
```

**(3)Cursor不变,装配的时装配相应的日期，所以dataColumns,viewIDs这两个参数需要加入时间**
```
    String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
        int[] viewIDs = { android.R.id.text1,R.id.text2 };
```
**(4)NotePadProvider中的insert方法和NoteEditor中的updateNote方法**
NotePadProvider中的insert方法中:
```
//修改时间
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
        format.setTimeZone(TimeZone.getTimeZone("GMT"));
        String dateTime = format.format(date);
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
    values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, dateTime);
}
// If the values map doesn't contain the modification date, sets the value to the current time.
if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
    values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);
}
```
NoteEditor中的updateNote方法中：
```
 //修改时间
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
        format.setTimeZone(TimeZone.getTimeZone("GMT"));
        String dateTime = format.format(date);
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);

```
  
#### 2、添加笔记查询功能（根据标题查询）：
**(1)list_options_menu.xml中新建一个查询的按钮:**
```
<item
        android:id="@+id/menu_search"
        android:title="@string/menu_search"
        android:icon="@android:drawable/ic_search_category_default"
        android:showAsAction="always">
```

**(2)NoteList中onOptionsItemSelected添加对应的case**
```
 //添加搜索
            case R.id.menu_search:
                startActivity(new Intent(Intent.ACTION_SEARCH,getIntent().getData()));
                return true;
```

**(3)AndroidManifest.xml**
```
        <!--添加搜索activity-->
        <activity
            android:name=".NoteSearch"
            android:label="@string/title_notes_search"
            android:theme="@android:style/Theme.Holo.Light">>
            <intent-filter>
                <action android:name="android.intent.action.NoteSearch" />
                <action android:name="android.intent.action.SEARCH" />
                <action android:name="android.intent.action.SEARCH_LONG_PRESS" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
                <!--1.vnd.android.cursor.dir代表返回结果为多列数据-->
                <!--2.vnd.android.cursor.item 代表返回结果为单列数据-->
            </intent-filter>
        </activity>
```

**(4)NoteSearch.java:**
```
package com.example.android.notepad;
import android.app.ListActivity;
import android.content.ContentUris;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.widget.ListView;
import android.widget.SearchView;
import android.widget.SimpleCursorAdapter;
/**
 * Created by c7 on 2019/5/15.
 */
public class NoteSearch  extends ListActivity  implements SearchView.OnQueryTextListener{
    private static final String[] PROJECTION = new String[]{
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//在这里加入了修改时间的显示
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_search);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        getListView().setOnCreateContextMenuListener(this);
        //为查询文本框注册监听器
        SearchView mSearchView = (SearchView)findViewById(R.id.search_view);//注册监听器
        mSearchView.setIconifiedByDefault(false); //显示搜索的天幕，默认只有一个放大镜图标
        mSearchView.setSubmitButtonEnabled(true); //显示搜索按钮
        mSearchView.setBackgroundColor(getResources().getColor(R.color.white)); //设置背景颜色
        mSearchView.setOnQueryTextListener(this);
    }
    @Override
    public boolean onQueryTextSubmit(String s) {
        return false;
    }
    @Override
    public boolean onQueryTextChange(String s) { //Test改变的时候执行的内容
        //Text发生改变时执行的内容
        String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";//查询条件
        String[] selectionArgs = { "%"+s+"%" };//查询条件参数，配合selection参数使用,%通配多个字符
        //查询数据库中的内容,当我们使用 SQLiteDatabase.query()方法时，就会得到Cursor对象， Cursor所指向的就是每一条数据。
        //managedQuery(Uri, String[], String, String[], String)等同于Context.getContentResolver().query()
        Cursor cursor = managedQuery(
                getIntent().getData(),            // Use the default content URI for the provider.用于ContentProvider查询的URI，从这个URI获取数据
                PROJECTION,                       // Return the note ID and title for each note. and modifcation date.用于标识uri中有哪些columns需要包含在返回的Cursor对象中
                selection,                        // 作为查询的过滤参数，也就是过滤出符合selection的数据，类似于SQL的Where语句之后的条件选择
                selectionArgs,                    // 查询条件参数，配合selection参数使用
                NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.查询结果的排序方式，按照某个columns来排序，例：String sortOrder = NotePad.Notes.COLUMN_NAME_TITLE
        );
       //一个简单的适配器，将游标中的数据映射到布局文件中的TextView控件或者ImageView控件中
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
        int[] viewIDs = { android.R.id.text1 , R.id.text2 };
        SimpleCursorAdapter adapter = new SimpleCursorAdapter(
                this,                   //context:上下文
                R.layout.noteslist_item,         //layout:布局文件，至少有int[]的所有视图
                cursor,                          //cursor：游标
                dataColumns,                     //from：绑定到视图的数据
                viewIDs                          //to:用来展示from数组中数据的视图
                //flags：用来确定适配器行为的标志，Android3.0之后淘汰
        );
        setListAdapter(adapter);
        return true;
    }
}
```
### 扩展功能：
#### 1、UI美化、更改记事本的背景：主要是对便签换了主题
**界面换一个Theme**
```
android:theme="@android:style/Theme.Holo.Light"
```

#### 2、导出笔记
**(1)editor_options_menu.xml:在菜单文件中添加一个导出笔记的选项**
```
 <item android:id="@+id/menu_output"
        android:title="@string/menu_output" />
```
**(2)NoteEditor.java中onOptionsItemSelected()方法添加对应的case**
```
 //导出笔记选项
            case R.id.menu_output:
                outputNote();
                break;
```
**(3)NoteEditor.java:添加outputNote()函数**
```
 //跳转导出笔记的activity，将uri信息传到新的activity
    private final void outputNote() {
        Intent intent = new Intent(null,mUri);
        intent.setClass(NoteEditor.this,OutputText.class);
        NoteEditor.this.startActivity(intent);
    }
```
**(4)OutputText.java:**
```
package com.example.android.notepad;

import android.app.Activity;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;

/**
 * Created by c7 on 2019/5/15.
 */

public class OutputText extends Activity {
    //要使用的数据库中笔记的信息
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_NOTE, // 2
            NotePad.Notes.COLUMN_NAME_CREATE_DATE, // 3
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 4
    };
    //读取出的值放入这些变量
    private String TITLE;
    private String NOTE;
    private String CREATE_DATE;
    private String MODIFICATION_DATE;
    //读取该笔记信息
    private Cursor mCursor;
    //导出文件的名字
    private EditText mName;
    //NoteEditor传入的uri，用于从数据库查出该笔记
    private Uri mUri;
    //关于返回与保存按钮的一个特殊标记，返回的话不执行导出，点击按钮才导出
    private boolean flag = false;
    private static final int COLUMN_INDEX_TITLE = 1;
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.output_text);
        mUri = getIntent().getData();
        mCursor = managedQuery(
                mUri,        // The URI for the note that is to be retrieved.
                PROJECTION,  // The columns to retrieve
                null,        // No selection criteria are used, so no where columns are needed.
                null,        // No where columns are used, so no where values are needed.
                null         // No sort order is needed.
        );
        mName = (EditText) findViewById(R.id.output_name);
    }
    @Override
    protected void onResume(){
        super.onResume();
        if (mCursor != null) {
            // The Cursor was just retrieved, so its index is set to one record *before* the first
            // record retrieved. This moves it to the first record.
            mCursor.moveToFirst();
            //编辑框默认的文件名为标题，可自行更改
            mName.setText(mCursor.getString(COLUMN_INDEX_TITLE));
        }
    }
    @Override
    protected void onPause() {
        super.onPause();
        if (mCursor != null) {
            //从mCursor读取对应值
            TITLE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE));
            NOTE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_NOTE));
            CREATE_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE));
            MODIFICATION_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE));
            //flag在点击导出按钮时会设置为true，执行写文件
            if (flag == true) {
                write();
            }
            flag = false;
        }
    }
    public void OutputOk(View v){
        flag = true;
        finish();
    }
    private void write()
    {
        try
        {
            // 如果手机插入了SD卡，而且应用程序具有访问SD的权限
            if (Environment.getExternalStorageState().equals(
                    Environment.MEDIA_MOUNTED)) {
                // 获取SD卡的目录
                File sdCardDir = Environment.getExternalStorageDirectory();
                //创建文件目录
                File targetFile = new File(sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt");
                //写文件
                PrintWriter ps = new PrintWriter(new OutputStreamWriter(new FileOutputStream(targetFile), "UTF-8"));
                ps.println(TITLE);
                ps.println(NOTE);
                ps.println("创建时间：" + CREATE_DATE);
                ps.println("最后一次修改时间：" + MODIFICATION_DATE);
                ps.close();
                Toast.makeText(this, "保存成功,保存位置：" + sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt", Toast.LENGTH_LONG).show();
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}
```
**(5)output_text.xml:**
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:paddingLeft="6dip"
    android:paddingRight="6dip"
    android:paddingBottom="3dip">
    <EditText android:id="@+id/output_name"
        android:maxLines="1"
        android:layout_marginTop="2dp"
        android:layout_marginBottom="15dp"
        android:layout_width="wrap_content"
        android:ems="25"
        android:layout_height="wrap_content"
        android:autoText="true"
        android:capitalize="sentences"
        android:scrollHorizontally="true" />

    <Button android:id="@+id/output_ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right"
        android:text="@string/output_ok"
        android:onClick="OutputOk" />
</LinearLayout>
```
**(6)AndroidManifest.xml中将这个Acitvity主题定义为对话框样式，并且加入权限：**
```
 <!--添加导出activity-->
        <activity android:name=".OutputText"
            android:label="@string/output_name"
            android:theme="@android:style/Theme.Holo.Dialog"
            android:windowSoftInputMode="stateVisible">
        </activity>
    </application>
    <!-- 在SD卡中创建与删除文件权限 -->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
    <!-- 向SD卡写入数据权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
#### 3、对笔记进行排序：根据修改时间和创建时间排序
**(1)list_options_menu.xml中添加**
```
<item
        android:id="@+id/menu_sort"
        android:title="@string/menu_sort"
        android:icon="@android:drawable/ic_menu_sort_by_size"
        android:showAsAction="always" >
        <menu>
            <item
                android:id="@+id/menu_sort1"
                android:title="@string/menu_sort1"/>
            <item
                android:id="@+id/menu_sort2"
                android:title="@string/menu_sort2"/>
        </menu>
    </item>
```
**(2)NoteList.java声明变量**
```
  private SimpleCursorAdapter adapter;
    private Cursor cursor;
    private String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
    private int[] viewIDs = { android.R.id.text1 , R.id.text2};
```
**(3)NoteList.java:onOptionsItemSelected添加对应的case**
```
  //创建时间排序
            case R.id.menu_sort1:
                cursor = managedQuery(
                        getIntent().getData(),
                        PROJECTION,
                        null,
                        null,
                        NotePad.Notes._ID
                );
                adapter = new SimpleCursorAdapter(
                        this,
                        R.layout.noteslist_item,
                        cursor,
                        dataColumns,
                        viewIDs
                );
                setListAdapter(adapter);
                return true;
            //修改时间排序
            case R.id.menu_sort2:
                cursor = managedQuery(
                        getIntent().getData(),
                        PROJECTION,
                        null,
                        null,
                        NotePad.Notes.DEFAULT_SORT_ORDER
                );
                 adapter = new SimpleCursorAdapter(
                        this,
                        R.layout.noteslist_item,
                        cursor,
                        dataColumns,
                        viewIDs
                );
                setListAdapter(adapter);
                return true;
```
#### 4、对字体大小颜色进行改变
**(1)editor_options_menu.xml:添加对应的菜单**
```
   <item
        android:id="@+id/font_size"
        android:title="@string/font_size">
        <!--子菜单-->
        <menu>
            <!--定义一组单选菜单项-->
            <group>
                <!--定义多个菜单项-->
                <item
                    android:id="@+id/font_10"
                    android:title="@string/font10"
                    />

                <item
                    android:id="@+id/font_16"
                    android:title="@string/font16" />
                <item
                    android:id="@+id/font_20"
                    android:title="@string/font20" />
            </group>
        </menu>
    </item>

    <item
        android:title="@string/font_color"
        android:id="@+id/font_color"
        >
        <menu>
            <!--定义一组普通菜单项-->
            <group>
                <!--定义两个菜单项-->
                <item
                    android:id="@+id/red_font"
                    android:title="@string/red_title" />
                <item
                    android:title="@string/black_title"
                    android:id="@+id/black_font"/>
            </group>
        </menu>
    </item>
```
**(2)NoteEditor.java中onOptionsItemSelected中添加对应的case**
```
            case R.id.font_10:
                mText.setTextSize(20);
                break;
            case R.id.font_16:
                mText.setTextSize(32);
                break;
            case R.id.font_20:
                mText.setTextSize(40);
                break;
            case R.id.red_font:
                mText.setTextColor(Color.RED);
                break;
            case R.id.black_font:
                mText.setTextColor(Color.BLACK);
                break;
```

**(3)因在下次进入时还是原来的字体大小，所以在数据库中加入相应的参数来保存字体大小和颜色的信息
NotePadProvider.java：**
```
@Override
       public void onCreate(SQLiteDatabase db) {
           db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
                   + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
                   + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
                   + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER"
                  + NotePad.Notes.COLUMN_NAME_TEXT_COLOR + "INTEGER" //新增加的修改字体颜色
                   + NotePad.Notes.COLUMN_NAME_TEXT_SIZE + "INTEGER" //新增加的修改字体大小
                   + ");");
       }
```

### (2)结果截图：
### 基本功能：
#### 1、NoteList中显示条目增加时间戳显示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191134800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

#### 2、添加笔记查询功能（根据标题查询）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191200783.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

### 扩展功能：
#### 1、UI美化、更改记事本的背景：主要是对便签换了主题
将所有界面都换成白色：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191305959.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191353755.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

#### 2、导出笔记
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191411722.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191424826.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191515467.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191540663.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191647128.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

#### 3、对笔记进行排序：根据修改时间和创建时间排序：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191717294.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

**根据创建时间排序：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191806430.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

**根据修改时间排序：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191822380.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

#### 4、对字体大小颜色进行改变
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191857492.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191836893.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191916746.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191931655.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190516191944433.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZpdmljNw==,size_16,color_FFFFFF,t_70)

## 三、总结
   NoteEditor类是用来修改记事条目标题的类，NotePad是用来声名Static常量的类，NotePadProvider是ContentProvider类，也是最重要的一个类，NoteSearch是增加的用来实现搜索的类，NoteList是显示在主页面上的内容，TitleEditor是用来编辑标题的类，OutputText类用来导出笔记。

