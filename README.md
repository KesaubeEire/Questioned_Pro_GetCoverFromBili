#GetCoverFromBili请教原作者~
您好UP,这里是我的原码,为了以防您不方便下载和运行在机器上,我在这里贴上主要代码供您查看
压缩包为源文件,平台是Android Studio
##MainActivity.java
```
package com.kesoft.kesa.get_bilibili_displayimage;

import android.annotation.SuppressLint;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.app.*;
import android.graphics.*;
import android.os.*;
import android.view.*;
import android.widget.*;

import java.io.ByteArrayOutputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class MainActivity extends AppCompatActivity
{

    EditText etNum;
    ImageView ivCover;
    Bitmap bmap;

    //==============================================

    //==============================================
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        etNum = (EditText) findViewById(R.id.et_av_number);
        ivCover = (ImageView) findViewById(R.id.iv_cover);


    }

    //==============================================
    @SuppressLint("WrongConstant")
    public void toast(String str)
    {
        Toast.makeText(this, str, 0).show();
    }

    void getCover(View v)
    {
        System.out.println("获取图片");
        new CoverGetterTask().execute(Integer.parseInt(etNum.getText().toString()));

    }

    void saveCover(View v)
    {
        System.out.println("保存图片");
        SaveBitMapTo(bmap);
    }

    //==============================================
    class CoverGetterTask extends AsyncTask<Integer, String, Bitmap>
    {
        public static final String bilibili_basr_url = "http://m.bilibili.com/video/av19093225";

        @Override
        protected Bitmap doInBackground(Integer[] p1)
        {
            String url = String.format(bilibili_basr_url, p1[0]);
            String html = getHtml(url);
            //正则表达式???
            //TODO:弄懂正则表达式的意义,作用和使用方式
            //TODO:==========================================================
            //TODO:这里的网址应该是用Android客户端访问的网址,不应该用这个Mac端的网址的
            //TODO:所以先标着,回来改
            Pattern p = Pattern.compile("class=\"<meta property=\"og: image\" content=\"([^\"]*)\"");
//            Pattern p = Pattern.compile("class=\"<meta data-vue-meta=\"true\" itemprop=\"image\" content=\"([^\"]*)\"");
            Matcher m = p.matcher(html);
            m.find();
//            String coverUrl = m.group(1);
            String coverUrl = m.group(1).replace("@400w_300h.jpg", "");
            publishProgress("封面<meta property=\"og: image\" content=|的链接是:" + coverUrl);
            return getBitmap(coverUrl);

        }

        @Override
        protected void onProgressUpdate(String... values)
        {
            super.onProgressUpdate(values);
            toast(values[0]);
        }

        @Override
        protected void onPostExecute(Bitmap bitmap)
        {
            super.onPostExecute(bitmap);
            if (bitmap == null) toast("获取失败");
            else
            {
                ivCover.setImageBitmap(bitmap);
                MainActivity.this.bmap = bitmap;
            }
        }
    }

    byte[] getByteFrom(String strUrl)
    {
        byte[] bytes = new byte[1024];
        int len = 0;
        try
        {
            URL url = new URL(strUrl);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            InputStream in = conn.getInputStream();
            ByteArrayOutputStream out = new ByteArrayOutputStream();

            while ((len = in.read(bytes)) != -1)
            {
                out.write(bytes, 0, len);
            }
            return out.toByteArray();
        } catch (Exception e)
        {
            e.printStackTrace();
        }

        return null;

    }

    String getHtml(String url)
    {
        return new String(getByteFrom(url));
    }

    Bitmap getBitmap(String url)
    {
        byte[] bytes = getByteFrom(url);
        return BitmapFactory.decodeByteArray(bytes, 0, bytes.length);
    }

    void SaveBitMapTo(Bitmap b)
    {
        try
        {
            FileOutputStream out = new FileOutputStream("/sdcard/封面.png");
            bmap.compress(Bitmap.CompressFormat.PNG, 100, out);
        } catch (FileNotFoundException e)
        {
            e.printStackTrace();
        }
    }

}


```

##activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.kesoft.kesa.get_bilibili_displayimage.MainActivity"
        android:orientation="vertical"
        android:baselineAligned="false"
        android:gravity="center|top">
    <EditText
            android:text="18542402"
            android:id="@+id/et_av_number"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:digits="0,1,2,3,4,5,6,7,8,9"
            android:hint="请输入av号"
            android:layout_margin="16dp"
    />
    <Button
            android:id="@+id/btn_getCover"
            android:onClick="getCover"
            android:text="@string/getCover"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
    />
    <Button
            android:id="@+id/btn_saveCover"
            android:onClick="saveCover"
            android:text="@string/saveCover"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
    />
    <ImageView
            android:layout_margin="16dp"
            android:id="@+id/iv_cover"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
    />

</LinearLayout>

```

##AndroidManinfest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.kesoft.kesa.get_bilibili_displayimage">

    <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>

</manifest>
```

非常感谢您的指导~
