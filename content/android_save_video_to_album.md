+++
title = "android 10 分区存储适配之保存到相册"
author = ["Donghai Ruan"]
date = 2021-01-08T17:31:00+08:00
lastmod = 2021-01-08T22:32:47+08:00
tags = ["android10", "保存到相册", "分区存储"]
categories = ["note"]
draft = false
+++

## 问题描述 {#问题描述}

当 targetSdkVersion 设置高于 29 后，分享拷贝失败


## 问题分析 {#问题分析}


### android 10 以后采用[分区存储权限说明](https://developer.android.com/training/data-storage/shared/media) {#android-10-以后采用-分区存储权限说明}

-   对权限申请做限制，否则在 android10 以下的设备会获取权限失败

<!--listend-->

```java
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
                 android:maxSdkVersion="28" />
```

| 设备       | 权限添加 maxSdkVersion="28" | 未添加 maxSdkVersion="28" |
|----------|-------------------------|------------------------|
| android 6  | yes                     | no                     |
| android 10 | yes                     | yes                    |
| android 11 | yes                     | yes                    |

-   对 android 10 以后不进行申请 WRITE\_EXTERNAL\_STORAGE，会一直返回失败


### 相对路径于 DCIM 会导致一些手机拷贝失败 {#相对路径于-dcim-会导致一些手机拷贝失败}

已知问题设备：小米 8

```java
values.put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DCIM)
```


## 解决方案 {#解决方案}


### 修改 compileSdkVersion 和 targetSdkVersion 为 29 {#修改-compilesdkversion-和-targetsdkversion-为-29}


### 权限声明 {#权限声明}

```java
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
                 android:maxSdkVersion="28" />
```


### 核心 java 代码 {#核心-java-代码}

```java
public static boolean saveVideoToAlbumIfNeed(final String path, final String pathToAlbum) {
        File file = new File(path);
    Uri uri = null;

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q && getTargetSdkVersion() >= Build.VERSION_CODES.Q) {
        // 通过插入到相册的方案
        ContentValues values = new ContentValues();
        values.put(MediaStore.MediaColumns.TITLE, file.getName());
        values.put(MediaStore.MediaColumns.DISPLAY_NAME, file.getName());
        values.put(MediaStore.MediaColumns.MIME_TYPE, "video/mp4");
//            values.put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DCIM);
        ContentResolver contentResolver = currentActivity.getContentResolver();
        uri = contentResolver.insert(MediaStore.Video.Media.EXTERNAL_CONTENT_URI, values);
        if(uri == null){
            Log.d(TAG, "Insert file to resolver failed.");
            return false;
        }

        // 拷贝到指定uri,如果没有这步操作，android11不会在相册显示
        try {
            OutputStream out = currentActivity.getContentResolver().openOutputStream(uri);
            FileUtil.copyFile(path, out);
        } catch (IOException e) {
            e.printStackTrace();
        }
    } else {
        String savePath = null;
        savePath = getSDCardPath() + "/" + pathToAlbum;

        // 文件已存在不重新拷贝
        File saveFile = new File(savePath);
        if (saveFile.exists()) {
            return true;
        }

        // 如果目录不存在先创建
        String saveRoot = savePath.substring(0, savePath.lastIndexOf("/"));
        File fileRoot = new File(saveRoot);
        if (!fileRoot.exists()) {
            fileRoot.mkdir();
        }

        // 拷贝文件
        if (! FileUtil.copyFile(path, savePath)) {
            Log.d(TAG, "Copy file to sdcard fail.");
            return false;
        }

        uri = Uri.fromFile(saveFile);
    }

    // 通知刷新相册
    Intent scanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
    scanIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
    scanIntent.setData(uri);
    currentActivity.sendBroadcast(scanIntent);
    return true;
}

public static boolean copyFile(String oldPath, OutputStream out) {
    Log.d(TAG, "oldPath = " + oldPath);
    try {
        int bytesum = 0;
        int byteread = 0;
        File oldfile = new File(oldPath);
        if (oldfile.exists()) {
            // 读入原文件
            InputStream inStream = new FileInputStream(oldPath);
            byte[] buffer = new byte[1444];
            while ((byteread = inStream.read(buffer)) != -1) {
                bytesum += byteread; //字节数 文件大小
                System.out.println(bytesum);
                out.write(buffer, 0, byteread);
            }
            inStream.close();
            out.close();
            return true;
        }
        else {
            Log.w(TAG, String.format("文件(%s)不存在。", oldPath));
        }
    }
    catch (Exception e) {
        Log.e(TAG, "复制单个文件操作出错");
        e.printStackTrace();
    }

    return false;
}
```
