# 动态注入参数到Manifest中


- 1:  


```
android {
    defaultConfig {
        manifestPlaceholders = [hostName:"www.example.com"]
    }
    ...
}
```

```
<intent-filter ... >
    <data android:scheme="http" android:host="${hostName}" ... />
    ...
</intent-filter>
```


- 2:  

```
android {
    defaultConfig {
        applicationId "com.example.myapp"
    }
    productFlavors {
        free {
            applicationIdSuffix ".free"
        }
        pro {
            applicationIdSuffix ".pro"
        }
    }
}
```

```
<intent-filter ... >
    <action android:name="${applicationId}.TRANSMOGRIFY" />
    ...
</intent-filter>
```

