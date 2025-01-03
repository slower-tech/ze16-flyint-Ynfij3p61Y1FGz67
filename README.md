**目录**

* [序:续《KeePass：密码管理工具》](#_label0)
* [概述](#_label1)
+ [KeePass Java 客户端 ( KeePassJava2 )](#_lab2_1_0)
+ [KeePassJava2 的模块结构](#_lab2_1_1)
+ [KeePassJava2 与 KeePass 的项目渊源](#_lab2_1_2)
+ [KeePass 的核心概念：Database(密码库) \> Group(\[多层]分组) \> Entry (密码条目)](#_lab2_1_3)
+ [KeePass 文件版本(v1/v2/v3\.1/v4\.x)](#_lab2_1_4)

* [使用示范](#_label2)
+ [Step0 使用 KeePass 终端创建密码库和密码条目](#_lab2_2_0)
+ [Step1 引入依赖](#_lab2_2_1)
+ [Step2 读取密码库文件(File/Database)、分组(Group)、密码条目(Entry)](#_lab2_2_2)

* [X 参考文献](#_label3)


[回到顶部(Back to Top)](#_labelTop)# 序:续《KeePass：密码管理工具》


* [\[密码管理/信息安全] 密码管理工具：KeePass vs LastPass vs 1Password \- 博客园/千千寰宇](https://github.com) 【推荐】


[回到顶部(Back to Top)](#_labelTop)# 概述


## KeePass Java 客户端 ( KeePassJava2 )


* `KeePassJava2` 是一个用`Java`编写的API，用于操作`KeePass`密码数据库



> * KeePassJava2 是一个高度灵活且高效的Java库，专门设计用于与KeePass `2.x`密码数据库交互。
> * 它全面支持阅读和写入KeePass文件版本`3`和`4`，并能读取版本`1.x`的数据库。
> * 该库精心构建，确保了在`Java 8`及更高版本，包括`Java`/`Android`环境中的广泛应用。
> * 通过不依赖特定的Java Cryptography Extension (JCE)策略文件，KeePassJava2保持轻量级且易于集成，实现了跨平台的兼容性。
> * 其**开放源码许可**（`Apache 2.0`）鼓励广泛的应用和定制。



> > Slogan : "Java API for KeePass Password Databases \- Read/Write 2\.x (File versions 3 and 4\), Read 1\.x"


* `KeePassJava2`作为一个库，并不直接运行一个独立的应用程序，因此没有传统意义上的“**启动文件**”。



> 它的使用**通常是嵌入到其他Java应用中**。
> 开发者会在其应用的主类中或特定的服务初始化部分，通过引入KeePassJava2的依赖和调用相关API来开启密码数据库的管理和访问功能。
> 比如，典型的启动逻辑可能出现在应用的入口点或者服务加载阶段，涉及创建KdbxCreds对象和加载数据库的操作。


## KeePassJava2 的模块结构


![](https://raw.githubusercontent.com/jorabin/KeePassJava2/master/ModuleStructure.svg)


## KeePassJava2 与 KeePass 的项目渊源


* KeePassJava2 项目的名称由 KeePass 的作者 Dominik Reichl 友情授权。与 KeePass 项目无正式联系。
* KeePass 实际上是由 Dominik 编写的代码定义的，这些代码是他创建和维护该项目时编写的。


## KeePass 的核心概念：Database(密码库) \> Group(\[多层]分组) \> Entry (密码条目)


* **密码数据库(Database)**被建模为三层抽象
* 数据库(Database)是记录的集合，其物理表示形式只需能够呈现为流即可。
* 条目(Entry)保存数据库中有价值的信息，而组允许将条目构建为集合(Collection)，就像文件夹结构一样。
* 数据库有一个根组(RootGroup)，通过跟踪根组的子组(Sub Group)可以浏览数据库的树形结构。
* 条目属于组(Group)。条目可以在组之间移动，组也可以在组之间移动。
* 但是，在一个数据库中创建的条目和组不能在未转换的情况下移动到另一个数据库：



```
database.newEntry(entryToCopy);
database.newGroup(groupToCopy);

```

## KeePass 文件版本(v1/v2/v3\.1/v4\.x)


* 3\.1 vs. 4


![](https://raw.githubusercontent.com/jorabin/KeePassJava2/master/KdbxDiagram.svg)


[回到顶部(Back to Top)](#_labelTop):[蓝猫加速器vqn下载](https://sanmenqingxie.com)# 使用示范


* JDK 版本



> 从release 2\.2开始，它需要Java 1\.8。早期版本需要Java 1\.7。


## Step0 使用 KeePass 终端创建密码库和密码条目



> 略


![](https://img2024.cnblogs.com/blog/1173617/202501/1173617-20250102174408448-514473645.png)


## Step1 引入依赖



```
    <dependencies>
        
        <dependency>
            <groupId>org.linguafranca.pwdbgroupId>
            <artifactId>KeePassJava2artifactId>
            <version>2.2.1version>
        dependency>
        <dependency>
            <groupId>org.linguafranca.pwdbgroupId>
            <artifactId>testartifactId>
            <version>2.2.1version>
        dependency>
	dependencies>

    <repositories>
        <repository>
            <id>oss.sonatype.org-snapshotid>
            <url>https://oss.sonatype.org/content/repositories/snapshotsurl>
            <releases>
                <enabled>falseenabled>
            releases>
            <snapshots>
                <enabled>trueenabled>
            snapshots>
        repository>
    repositories>

```

## Step2 读取密码库文件(File/Database)、分组(Group)、密码条目(Entry)



```
package com;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.linguafranca.pwdb.Credentials;
import org.linguafranca.pwdb.Database;
import org.linguafranca.pwdb.Entry;
import org.linguafranca.pwdb.Group;
import org.linguafranca.pwdb.kdb.KdbCredentials;
import org.linguafranca.pwdb.kdbx.KdbxCredentials;
import org.linguafranca.pwdb.kdbx.KdbxCreds;
import org.linguafranca.pwdb.kdbx.KdbxStreamFormat;
import org.linguafranca.pwdb.kdbx.dom.DomDatabaseWrapper;
//import org.linguafranca.pwdb.kdbx.jackson.JacksonDatabase;
import org.linguafranca.pwdb.kdbx.jaxb.JaxbDatabase;
import org.linguafranca.pwdb.kdbx.simple.SimpleDatabase;
import org.linguafranca.pwdb.kdbx.simple.model.KeePassFile;
import org.linguafranca.pwdb.security.CipherAlgorithm;

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.util.*;

/**
 * KeePass 数据读取
 * @note
 *  KeePassJava2 项目的名称由 KeePass 的作者 Dominik Reichl 友情授权。与 KeePass 项目无正式联系。
 *  KeePass 实际上是由 Dominik 编写的代码定义的，这些代码是他创建和维护该项目时编写的。
 * @reference-doc
 *  [1] https://github.com/jorabin/KeePassJava2
 *  [2] https://github.com/jorabin/KeePassJava2/blob/master/example/src/main/java/org/linguafranca/pwdb/kdbx/QuickStart.java
 */
@Slf4j
public class KeePassExample {
    @SneakyThrows
    public static void main(String[] args) {
        String keepassFilePath = "D:\\Program-Data\\KeePass-Data\\xxxx-resource-accounts.kdbx";
        byte [] passwordBytes = "123".getBytes();

        //KdbxCreds credentials = new KdbxCreds("JNY#1638".getBytes());//[x]
        KdbCredentials credentials = new KdbCredentials.Password( passwordBytes );

        //InputStream inputStream = KeePassExample.class.getClass().getClassLoader().getResourceAsStream("test1.kdbx");
        InputStream keepassFileInputStream = new FileInputStream( new File(keepassFilePath) );

        /**
         * 密码数据库被建模为三层抽象
         * 数据库(Database)是记录的集合，其物理表示形式只需能够呈现为流即可。
         * 条目(Entry)保存数据库中有价值的信息，而组允许将条目构建为集合(Collection)，就像文件夹结构一样。
         * 数据库有一个根组(RootGroup)，通过跟踪根组的子组(Sub Group)可以浏览数据库的树形结构。
         *  条目属于组(Group)。条目可以在组之间移动，组也可以在组之间移动。
         *  但是，在一个数据库中创建的条目和组不能在未转换的情况下移动到另一个数据库：
         *    database.newEntry(entryToCopy);
         *    database.newGroup(groupToCopy);
         */
        Database database = DomDatabaseWrapper.load(new KdbxCreds( passwordBytes ), keepassFileInputStream);
        //不同的 Database 实现具有不同的特性，主要提现在速度上 | 例如 对于 JAXB 和 Simple 来说，加载时间是主要因素，而对于 DOM 实现来说，数据库遍历是主要因素
        //Database database = SimpleDatabase.load(credentials, keepassFileInputStream);
        //Database database = JaxbDatabase.load(credentials, keepassFileInputStream);
        //Database database = DomDatabaseWrapper.load(credentials, keepassFileInputStream);
        //Database database = JacksonDatabase.load(credentials, keepassFileInputStream);

        KdbxStreamFormat streamFormat = (KdbxStreamFormat) database.getStreamFormat();
        int databaseVersion = streamFormat.getStreamConfiguration().getVersion();//4
        CipherAlgorithm cipherAlgorithm = streamFormat.getStreamConfiguration().getCipherAlgorithm();//"AES"


        String databaseName = database.getName();
        Group rootGroup = database.getRootGroup();
        List generalGroupList = rootGroup.findGroups("General");//rootGroup.getGroups();
        Group generalGroup = generalGroupList.get(0);
        List groups = generalGroup.getGroups();
        int groupsSize = groups.size();
        log.info("GeneralGroup's groups size:{}", groupsSize);
        if(groupsSize > 0){
            groups.stream().forEach(group -> {
                String groupName = group.getName();
                List entries = group.getEntries();// group.findEntries("mysql", true);
                int entriesSize = group.getEntriesCount();
                entries.stream().forEach(entry -> {
                    log.info("entry.path:{}", entry.toString() );///xxxx-resource-accounts-database/General/cn-dev/xxx-mysql
                    UUID uuid = entry.getUuid();
                    String path = entry.getPath();
                    String title = entry.getTitle();
                    String username = entry.getUsername();
                    String password = entry.getPassword();
                    String url = entry.getUrl();
                    String notes = entry.getNotes();
                    String cdcUserCustomField = entry.getProperty("CDC_USER");//自定义扩展字段
                    String cdcPasswordCustomField = entry.getProperty("CDC_PASSWORD");//自定义扩展字段
                    byte[] xxxCustomFileField = entry.getBinaryProperty("");
                });
            });
        }
    }
}

```

![](https://img2024.cnblogs.com/blog/1173617/202501/1173617-20250102172602480-1452674706.png)


[回到顶部(Back to Top)](#_labelTop)# X 参考文献


* [\[密码管理/信息安全] 密码管理工具：KeePass vs LastPass vs 1Password \- 博客园/千千寰宇](https://github.com) 【推荐】
* KeePass 【推荐】



> * [https://keepass.info/index.html](https://github.com)
> * [https://keepass.info/translations.html](https://github.com) (汉化包)
> * [https://github.com/jorabin/KeePassJava2](https://github.com)
> * [https://github.com/jorabin/KeePassJava2/blob/KeePassJava2\-2\.2\.1/example/src/main/java/org/linguafranca/pwdb/kdbx/QuickStart.java](https://github.com)
> * [https://github.com/jorabin/KeePassJava2/blob/KeePassJava2\-2\.2\.1/example/src/test/java/org/linguafranca/pwdb/kdbx/SimpleQuickStartTest.java](https://github.com)


* github topic



> * [https://github.com/topics/keepass](https://github.com)
> * [https://github.com/PhilippC/keepass2android](https://github.com) : 4\.9k star 2025\.1\.2
> 
> 
> 
> > * [https://play.google.com/store/apps/details?id\=keepass2android.keepass2android\&pli\=1](https://github.com)
> 
> 
> * [https://github.com/jorabin/KeePassJava2](https://github.com) : 254 star 2025\.1\.2
> * [https://mvnrepository.com/artifact/de.slackspace/openkeepass](https://github.com) : 153 个 star（相比 KeePassJava2 项目的 254 个 star ，star数偏少），且项目更新慢


* [探秘KeePassJava2：强大的跨平台密码管理器API \- CSDN](https://github.com)
* [KeePassJava2: 强大的Java密码管理库 \- CSDN](https://github.com)
* [KeePassJava2 使用指南 \- CSDN](https://github.com)
* [如何在Java中使用 KeePass数据库(kdbx) \- blog.bfw.wiki](https://github.com)



> [https://mvnrepository.com/artifact/de.slackspace/openkeepass](https://github.com) : 153 个 star（相比 KeePassJava2 项目的 254 个 star ，star数偏少），且项目更新慢


