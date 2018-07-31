1. 在jar包所在的下执行
```bash
mvn install:install-file -Dfile=sqljdbc4.jar -DgroupId=com.microsoft.sqlserver -DartifactId=sqljdbc4 -Dversion=4.0 -Dpackaging=jar
```


2. 在pom.xml 文件中添加
```xml
<dependency>
  <groupId>com.microsoft.sqlserver</groupId>
  <artifactId>sqljdbc4</artifactId>
  <version>4.0</version>
</dependency>
```