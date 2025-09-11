---
layout: post
title: 【Androidアプリ】外部アプリと連携してファイルを転送する方法
date: 2020-03-29
tags: [Android, Java, Kotlin]
---

# はじめに

お世話になります、hosochinです  
最近家でラーメン二郎ごっこやってお腹ぶっ壊しました

さて、今回はAndroidアプリについてです  
少し前に作っていたAndroidアプリで、zipファイルを外部アプリ（mail、Lineとか）使って転送する機能を実装しようとして、ハマったのでそのメモになります  
結論としては、Android7から『file://』による指定ができなくなったため、fileproviderでuriを作る必要がありました

# サンプルコード

## AndroidManifest.xml

```xml
<application>
    <!-- providerの設定 -->
    <!-- android:grantUriPermissionsをtrueにして外部からのファイルへのアクセスを許可する -->
    <provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.provider"
        android:exported="false"
        android:grantUriPermissions="true"> 
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/file_provider" />
    </provider>
</application>
```

## MainActivity.kt

```kotlin
// zipファイルのパス
val exportDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)
val zipFilePath = exportDir.path + "/sample.zip"

val intent = Intent()
intent.type = "application/zip"
intent.action = Intent.ACTION_SEND
intent.putExtra(
    Intent.EXTRA_STREAM, FileProvider.getUriForFile(
        this, applicationContext.packageName + ".provider"
        , File(zipFilePath)
    )
)
intent.addFlags(Intent.FLAG_GRANT_WRITE_URI_PERMISSION or Intent.FLAG_GRANT_READ_URI_PERMISSION)
startActivity(intent)
```

## res/xml/file_provider.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <external-path name="external_storage_directory" path="." />
</resources>
```

## external-pathの設定について

- **name**: 適当
- **path**: Environment.getExternalStorageDirectory()直下を指定するため`.`を設定

# まとめ

どうやらキモは `file_provider.xml` っぽい  
転送したい対象のファイルの場所によってxmlの書き方も変わるので以下にまとめておきます

## ファイル場所別の設定

| ファイルの場所 | 設定 |
|---|---|
| `Context.getFilesDir()` | `<files-path name="name" path="path" />` |
| `getCacheDir()` | `<cache-path name="name" path="path" />` |
| `Environment.getExternalStorageDirectory()` | `<external-path name="name" path="path" />` |
| `Context.getExternalFilesDir(null)` | `<external-files-path name="name" path="path" />` |
| `Context.getExternalCacheDir()` | `<external-cache-path name="name" path="path" />` |

Android 7.0 (API level 24) からファイルへの直接アクセスが制限されたため、FileProviderを使用する必要があります。適切な設定を行うことで、外部アプリとのファイル共有が安全に実行できます。
