
Gradle 2012年推出，是项目自动化构建工具。

```
task hello << {
   println 'hello world'
}
```

-  外部依赖  

```
dependencies{
    ...
}
```

- 储存库  

```  
repositories {
     mavenCentral()
     
}
```

# 插件引用
1.脚本插件  

```
apply from: 'other.gradle'
```

2.二进制插件  

```
apply plugin: 'com.android.application'
apply plugin: 'me.tatarka.retrolambda'
apply plugin: 'com.neenbedankt.android-apt'
```

3.自定义插件  

```
apply plugin: HelloPlugin

class HelloPlugin implements Plugin<Project> {
   void apply(Project project) {
      project.task('hello') << {
         println "Hello from the HelloPlugin."
      }
   }
}
```

Mac/Linux上运行  

```
./gradlew taskName
```



