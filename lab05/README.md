# Лабораторная работа №5. Облачные базы данных. Amazon RDS, DynamoDB

##### в течении 12 дней aws аккаунт был заблокирован :( 

![](https://i.imgur.com/JeTvb4k.png)

____________

## [Link](https://github.com/MSU-Courses/cloud-computing/tree/main/_lab/05_Cloud_DB)
_______

## Реализация

## Подготовка среды (VPC/подсети/SG)

![](https://i.imgur.com/oTwbna9.png)

### --- 1 Создаем VPC с 2-мя pub и 2-мя private подсетями в разных зонах доступности ---

![](https://i.imgur.com/kSDuldC.png)

#### --- 1 NAT на весь регион (автодоступ приватных подсетей к инету автоматически обеспечивает AWS) ---
![](https://i.imgur.com/wbFKlX1.png)

![](https://i.imgur.com/CryHzUt.png)

### --- SG ---

**web group**

![](https://i.imgur.com/yJN3lWi.png)

**mysql group**

![](https://i.imgur.com/ruC9Ih0.png)

только ресурсы, принадлежащие этой группе безопасности, смогут подключаться к базе данных ⬆️

![](https://i.imgur.com/61hpWJf.png)

теперь приложение сможет инициализировать соединение с бд ⬆️

_____

## Развертывание Amazon RDS 

> Что такое Subnet Group? И зачем необходимо создавать Subnet Group для базы данных?

Subnet Group — это набор подсетей в разных зонах доступности, объединённые для работы с базой данных.
он нужен для:
**Отказоустойчивость** — база данных автоматически размещается в нескольких зонах доступности, при сбое одной зоны сервис продолжает работать.
**Сетевая безопасность** — позволяет держать базу данных в приватных подсетях вашего VPC.
**Гибкость** — облегчает управление и изменение сетевой топологии базы данных.

#### --- создаем subnet группу для бд, выбирая только private подсети ---

![](https://i.imgur.com/ePoLTYS.png)

#### --- создаем экзепляр бд на сонове параметров из описания работы ---

![](https://i.imgur.com/33PagFX.png)

![](https://i.imgur.com/AQyIJLc.png)


/*
admin
project-rds-mysql-prod.cbca6umaah80.eu-central-1.rds.amazonaws.com
ainaioiOIAI
*/
_____


## Создание виртуальной машины для подключения к базе данных

#### --- создаем и настраиваем, как указано в описании работы ---


![](https://i.imgur.com/tPrnNzg.png)


![](https://i.imgur.com/oXMKZsZ.png)


#### --- подключение + базовые манипуляции ---

![](https://i.imgur.com/61ya6XD.png)


```bash
[ec2-user@ip-10-0-3-76 ~]$ mysql --version
mysql  Ver 15.1 Distrib 10.5.29-MariaDB, for Linux (x86_64) using  EditLine wrapper
```

клиент установлен!

```bash
[ec2-user@ip-10-0-3-76 ~]$ mysql -h TUT_RDS_ENDPOINT -u admin -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 38
Server version: 8.0.42 Source distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

#### --- ЗАПРОСЫ (данные были сгенерированы при помощи ИИ) ---

```bash
MySQL [project_db]> SELECT * FROM categories;
+----+-------+
| id | name  |
+----+-------+
|  1 | Work  |
|  2 | Home  |
|  3 | Study |
+----+-------+
3 rows in set (0.001 sec)

MySQL [project_db]> SELECT
    ->     todos.id,
    ->     todos.title,
    ->     categories.name AS category,
    ->     todos.status
    -> FROM todos
    -> JOIN categories ON todos.category_id = categories.id;
+----+------------------+----------+-------------+
| id | title            | category | status      |
+----+------------------+----------+-------------+
|  1 | Finish report    | Work     | in-progress |
|  5 | Fix server issue | Work     | pending     |
|  2 | Clean kitchen    | Home     | pending     |
|  4 | Buy groceries    | Home     | completed   |
|  3 | Prepare for exam | Study    | in-progress |
|  6 | Read textbook    | Study    | pending     |
+----+------------------+----------+-------------+
6 rows in set (0.001 sec)

MySQL [project_db]> SELECT
    ->     todos.id,
    ->     todos.title,
    ->     categories.name AS category,
    ->     todos.status
    -> FROM todos
    -> JOIN categories ON todos.category_id = categories.id;
+----+------------------+----------+-------------+
| id | title            | category | status      |
+----+------------------+----------+-------------+
|  1 | Finish report    | Work     | in-progress |
|  5 | Fix server issue | Work     | pending     |
|  2 | Clean kitchen    | Home     | pending     |
|  4 | Buy groceries    | Home     | completed   |
|  3 | Prepare for exam | Study    | in-progress |
|  6 | Read textbook    | Study    | pending     |
+----+------------------+----------+-------------+
6 rows in set (0.001 sec)

```
_____


## Создание Read Replica

#### --- РЕПЛИКА создавалась на базе параметров в описании рабооты ---

Read Replica — это копия нашей базы данных, предназначенная только для чтения, которая получает данные от основного (primary) экземпляра через репликацию. Её используют для разгрузки чтения. 

![](https://i.imgur.com/mzIq1m6.png)

ждем ⬆️

![](https://i.imgur.com/BHuQHF6.png)

радуемся ⬆️

#### --- ОТВЕТЫ НА ВОПРОСЫ ---

> Подключитесь к Read Replica с вашей виртуальной машины EC2 и выполните запросы на чтение данных (SELECT) из таблиц, созданных на основном экземпляре базы данных. Какие данные вы видите? Объясните почему.

вижу те же данные, что и в основной БД, тк Replica скопировала их 

> Попробуйте выполнить запрос на запись (INSERT/UPDATE) на реплике. Получилось ли выполнить запись на Read Replica? Почему?

не получилосб, тк Replica - только read-only и она не принимает записи, тк вприципе иначе логика ее существования нарушается

> Перейдите на основной экземпляр базы данных и добавьте новую запись в одну из таблиц. Вернитесь к подключению к Read Replica и выполните запрос на чтение. Отобразилась ли новая запись на реплике? Объясните почему.

новая запись появится (возможно с задержкой). это механизм асинхронной репликации, поэтому обновления происходят и отображаются и в реплико то же.

> Зачем нужны Read Replicas?

Read Replicas нужны в основном для масштабирования операций чтения — они снимают нагрузку с основного экземпляра, позволяя направлять тяжёлые аналитические запросы и отчёты на реплики. кроме того, можно создавать реплики в других регионах AWS для уменьшения задержки при доступе к данным из разных географических точек. они также пригодны для создания бэкапов и тестирования без влияния на production базу данных.

____

## Подключение приложения к базе данных

создам простой консольный Java-проект, использующий JDBC для работы с базой данных. реализую подключение к БД и базовые операции (CRUD), чтобы проверить корректность конфигурации и работу приложения.

___

```xml
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.33</version>
        </dependency>
    </dependencies>
```

зависимость для подключения к бд с mysql драйвером (наша бд)
___

класс, отвечающий за подключение к БД

```java
package utils;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DbConnector {

    private static final String USERNAME;

    private static final String DATABASE;

    private static final String PASS;

    static {
        USERNAME = "admin";
        DATABASE = "jdbc:mysql://project-rds-mysql-prod.cbca6umaah80.eu-central-1.rds.amazonaws.com:3306/project_db";
        PASS = "ainaioiOIAI";
    }

    public static Connection connect(){
        try(Connection conn = DriverManager.getConnection(DATABASE, USERNAME, PASS)) {
            System.out.println("DONE!");
            return conn;
        } catch (SQLException e) {
            System.out.println("PROBLEM! " + e.getMessage());
            throw new RuntimeException(e);
        }
    }
}

```

___

репозиторий с базовыми методами (которые и будем запускать)

```java
package repo;

import utils.DbConnector;

import java.sql.*;

public class GeneralRepository {
    public static void insertIntoTodos(String title, int category_id, String status){
        try(Connection connection = DbConnector.connect()){
            PreparedStatement statement = connection.prepareStatement(
                    "Insert Into todos (title, category_id, status) " +
                            "values (?,?,?)"
            );
            statement.setString(1, title);
            statement.setInt(2, category_id);
            statement.setString(3, status);
            statement.executeUpdate();
            statement.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void insertIntoCategories(String name){
        try(Connection connection = DbConnector.connect()){
            PreparedStatement statement = connection.prepareStatement(
                    "Insert Into categories (name) " +
                            "values (?)"
            );
            statement.setString(1, name);
            statement.executeUpdate();
            statement.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void getCategoriesAndTodos(){
        try(Connection connection = DbConnector.connect();
            Statement statement = connection.createStatement();
            ResultSet set = statement.executeQuery(
                    "select todos.id, todos.title, categories.name as category, todos.status from todos" +
                            " left join categories on todos.category_id = categories.id");
            )
        {
            while(set.next()){
                System.out.println(set.getInt("id") + " " + set.getString("title") + " " + set.getString("category") + " " + set.getString("status"));
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     =======================
     МЕТОДЫ ОБНОВЛЕНИЯ И ТД
     ======================= 
     */

}

```

___

точка входа

```java
import repo.GeneralRepository;
import utils.DbConnector;

import java.sql.SQLException;

public class Main {

    public static void main(String[] a) throws SQLException {
        /* сначала получим все до вставки */
        GeneralRepository.getCategoriesAndTodos();

        /* вставим новые записи */
        GeneralRepository.insertIntoCategories("NEW_CATEGORY");
        GeneralRepository.insertIntoTodos("NEW_TODO", 1, "pending");
        
        /* теперь получим все после вставки */
        GeneralRepository.getCategoriesAndTodos();
    }
}
```
___

далее необходимо создать deliverable jar файл для запуска в инстансе и проверки работы программы

#### --- УПАКОВАЛИ ---

```bash
C:\Program Files\JetBrains\IntelliJ IDEA 2025.2.1\plugins\maven\lib\maven3\bin\mvn.cmd -Didea.version=2025.2.1 -Dmaven.ext.class.path=C:\Program Files\JetBrains\IntelliJ IDEA 2025.2.1\plugins\maven\lib\maven-event-listener.jar -Djansi.passthrough=true -Dstyle.color=always -Dmaven.repo.local=C:\Users\Calculator\.m2\repository package -f pom.xml
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------------< aws:aws-demo >----------------------------
[INFO] Building aws-demo 1.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[WARNING] The artifact mysql:mysql-connector-java:jar:8.0.33 has been relocated to com.mysql:mysql-connector-j:jar:8.0.33: MySQL Connector/J artifacts moved to reverse-DNS compliant Maven 2+ coordinates.
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ aws-demo ---
[INFO] Copying 0 resource from src\main\resources to target\classes
[INFO] 
[INFO] --- compiler:3.13.0:compile (default-compile) @ aws-demo ---
[INFO] Recompiling the module because of changed source code.
[INFO] Compiling 3 source files with javac [debug target 23] to target\classes
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ aws-demo ---
[INFO] skip non existing resourceDirectory C:\Users\Calculator\TeachSyncApp\aws-demo\src\test\resources
[INFO] 
[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ aws-demo ---
[INFO] Recompiling the module because of changed dependency.
[INFO] 
[INFO] --- surefire:3.2.5:test (default-test) @ aws-demo ---
[INFO] 
[INFO] --- jar:3.4.1:jar (default-jar) @ aws-demo ---
[INFO] Building jar: C:\Users\Calculator\TeachSyncApp\aws-demo\target\aws-demo-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.616 s
[INFO] Finished at: 2025-11-29T21:34:33+02:00
[INFO] ------------------------------------------------------------------------
```


#### --- ИЗ PPK -> PEM ---

![](https://i.imgur.com/GjhPOCd.png)


#### --- ЗАКИДЫВАЕМ JAR файл ---

```bash
PS C:\Users\Calculator\TeachSyncApp\aws-demo\target> & "C:\Program Files\PuTTY\pscp.exe" -i "D:\Downloads\lab_06.ppk" ".\aws-demo-1.0-SNAPSHOT.jar" ec2-user@35.159.40.39:/home/ec2-user/                                                                                                                                         
aws-demo-1.0-SNAPSHOT.jar | 5 kB |   5.0 kB/s | ETA: 00:00:00 | 100%
```

#### --- запуск ---
```bash
Complete!
openjdk version "17.0.17" 2025-10-21 LTS
OpenJDK Runtime Environment Corretto-17.0.17.10.1 (build 17.0.17+10-LTS)
OpenJDK 64-Bit Server VM Corretto-17.0.17.10.1 (build 17.0.17+10-LTS, mixed mode, sharing)
[ec2-user@ip-10-0-3-76 ~]$ ls
aws-demo-1.0-SNAPSHOT.jar

```

барабанная дробь....

```bash
[ec2-user@ip-10-0-3-76 ~]$ java -jar aws-demo-1.0-SNAPSHOT.jar
no main manifest attribute, in aws-demo-1.0-SNAPSHOT.jar
```

в jar нет указания на запускаемый Main class, исправляем...

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>aws.demo.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

повторяем действия из пунктов выше и запускаем повторно

```bash
[ec2-user@ip-10-0-3-76 ~]$ rm aws-demo-1.0-SNAPSHOT.jar
[ec2-user@ip-10-0-3-76 ~]$ java -jar aws-demo-1.0-SNAPSHOT.jar
DONE!
1 Finish report Work in-progress
2 Clean kitchen Home pending
3 Prepare for exam Study in-progress
4 Buy groceries Home completed
5 Fix server issue Work pending
6 Read textbook Study pending
DONE!
DONE!
DONE!
1 Finish report Work in-progress
2 Clean kitchen Home pending
3 Prepare for exam Study in-progress
4 Buy groceries Home completed
5 Fix server issue Work pending
6 Read textbook Study pending
7 NEW_TODO Work pending -- НОВАЯ ЗАПИСЬ

```

```bash
Database changed
MySQL [project_db]> select * from categories
    -> ;
+----+--------------+
| id | name         |
+----+--------------+
|  1 | Work         |
|  2 | Home         |
|  3 | Study        |
|  4 | NEW_CATEGORY |
+----+--------------+
4 rows in set (0.002 sec)

```

категория так же добавилась

## ВЫВОД

если честно, одна из самых интересных лабораторных работ за всю историю моих студенческих лет! особенно интересно было работать с базой данных в облаке, на Amazon RDS, подключаться к ней с виртуальной машины EC2 и наблюдать, как данные обрабатываются удалённо.

при наличии свободного временни обязательно расширю функционал своего java проекта



