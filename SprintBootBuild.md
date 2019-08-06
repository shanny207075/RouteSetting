# 建立專案

## 配置

### 1.在eclipse建立一個gradle專案

![](./images/1-8f309bef-5655-45fa-a16a-9444aeae78cf.png)

![](./images/1-15bab749-8295-45e9-95dd-d2dfb8218758.png)

### 2.把src/main/java裡面的class package都砍掉

### 3.build.gradle 改內容

```Groovy
    // gradle脚本自身需要使用的资源
    // 一開始 buildscript 中紅色部份，設定 spring boot 版本，
    // 後面 dependencies (依賴) 的部份引入 spring boot 時，即會引入這個版本。
    buildscript {
     ext {
      springBootVersion = '1.5.18.RELEASE'
     }
     // jar檔案來源是從哪裡來
     repositories {
      mavenCentral()
     }
     // 需要那些依賴
     dependencies {
      classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
     }
    }
    
    // 引入java插件，但是其背後已經幫我們做了很多事情，
    // 比如它使得我們能夠運行gradle build命令。
    apply plugin: 'java'
    // 此時將生成Eclipse的.project工程文件。
    apply plugin: 'eclipse'
    apply plugin: 'org.springframework.boot'
     
    // JVM 版本號要求
    sourceCompatibility = 1.8
    targetCompatibility = 1.8
    
    // 自身需要的资源
    repositories {
     mavenCentral()
    }
    
    // 取得spring-cloud-starter-config (取得clinet端的依賴)
    dependencies {
     compile('org.springframework.boot:spring-boot-starter-web')
    
    // testCompile 是用來引入單元測試用的 jar 檔，
    // 這些 jar 檔只有開發階段進行單元測試時會用到，
    // 也不會被包入最後產生的 jar 檔裡。
     testCompile('org.springframework.boot:spring-boot-starter-test')
     
    }
    
    dependencyManagement {
     imports {
      mavenBom "org.springframework.cloud:spring-cloud-dependencies:Dalston.SR5"
     }
    }
```
```Groovy   
    apply plugin: 'java'
```  

**引入java插件**
* compileJava 編譯 Java 原始碼
* test 使用 JUnit 測試程式
* jar 打包成 JAR 檔案

### 4.gradle(STS)->Refresh All

下載spring 需要使用的jar包

### 5.建立bootstrap.yml與 application.yml

new->other->general->File
```Groovy 
    server:
    
      port: 40990
```

預設值會是8080

### 6.建立一個main的主程式，這是啟動spring boot的關鍵

![](./images/1-1bd4df91-9233-4a03-bbe2-2174cfadef96.png)

這個Application類便是Spring Boot程序的入口。

![](./images/1-9ebfbebd-0159-4b52-bd66-b5eba0562be3.png)

啟動server

![](./images/Untitled-474f7ffa-3b6a-42fd-b979-826b11f177a0.png)

## 開發

ThisIsBean並沒有綁任何Spring 的Annotation 所以Spring 不知道他是什麼東西，所以要在某個地方建立一個Spring Bean給他。spring 容器裡面就有一個bean 叫做thisIsBean物件

![](./images/Untitled-b062d482-e8a9-4059-851e-b7e10ddfe69f.png)

這樣就可以注入了。

因為ThisIsMain有綁Service Annotation 所以在自動掃描的時候，他已經是一個spring bean。

![](./images/Untitled-4cf4d019-d5a2-4710-87a2-b0ec914efeed.png)

進入程式

![](./images/Untitled-788d9cdc-7219-4a6f-adbe-a97d7a1812b7.png)
