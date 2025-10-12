### Джуринский Андрей, I2302
### Лабораторная работа №2.

### Цель

Познакомиться с основными вычислительными сервисами AWS, научиться создавать и настраивать виртуальные машины (EC2), а также развёртывать простые веб-приложения.

____________________________________________________

#### Задание 0. Подготовка среды


> Задание:
- Зарегистрируйтесь в AWS и создайте бесплатный аккаунт (Free Tier).
Перейдите по ссылке: https://aws.amazon.com/
- Нажмите "Create an AWS Account" и следуйте инструкциям.
Подробный процесс показан в теоретическом материале 02_AWS_Introduction
- Войдите в консоль управления под root-пользователем.
- В правом верхнем углу выберите регион EU (Frankfurt) eu-central-1.

> Выполнение:

был выполнен вход в root интерфейс, предоставляющий доступ к сервисам и ресурсам AWS аккаунта

![](/lab02/photos/image.png)

____________________________________________________
#### Задание 1. Создание IAM группы и пользователя

> Задание:

1) Откройте сервис IAM (Identity and Access Management).
2) Создайте IAM группу Admins:

- Перейдите в раздел "Groups" и нажмите "Create New Group".
Введите имя группы Admins и нажмите "Next Step".
- На шаге "Attach Policy" выберите политику AdministratorAccess.

> Что делает данная политика?

3) a) Создайте IAM пользователя с любым именем:
- Перейдите в раздел "Users" и нажмите "Add user".
- Введите имя пользователя, например cloudstudent.
- Привяжите пользователя к группе Admins.
- Разрешите пользователю доступ в AWS Management Console.
- Убедитесь, что пользователь создан и имеет доступ к консоли.
- Выйдите из консоли под root-пользователем и войдите под новым IAM пользователем.

> Выполнение

![alt text](/lab02/photos/image-1.png)

IAM - система политик, которая создана для управления пользователями разных групп, их доступам к ресурсам и настройки ьезопасности

> Что делает данная политика? (AdministratorAccess)

на фото выше по заданию для группы администраторовв была выбрана политика AdministratorAccess, которая подразумевает полный контроль пользователей группы к сервисам и ресурсам платформы AWS

далее необходимо создать пользователя и привязать его к группе `Admins`, разрешить ему доступ к консоли AWS, для работу внутри системы с ограничениями, которые связаны с назначенной политикой группы

> Выполнение

![alt text](/lab02/photos/image-2.png)

задал имя пользователя, дал доступ к консоли, доступ выдается через политику IAM, устанавливается пароль и убрал галочку с возможности смены пароля (просто так)

![alt text](/lab02/photos/image-3.png)

добавляем пользователя к группе

![alt text](/lab02/photos/image-4.png)

пользователь создан!

далее по заданию выходим из root консоли и входим под созданным пользователем

![alt text](/lab02/photos/image-5.png)

вход в систему успешный
____________________________________________________

#### Задание 2. Настройка Zero-Spend Budget

> задание

- Откройте сервис Billing and Cost Management.
- В меню слева выберите Budgets → Create budget.
- Выберите "Zero spend budget" шаблон и укажите следующие параметры:
    - Budget name: ZeroSpend
    - Email recipients: ваш email
2) Нажмите "Create budget" внизу страницы.

После создания данного бюджета вы будете получать уведомления, если ваши расходы превысят $0.


> выполнение

данная система позволяет информировать о любых плятежных действиях. функция не блокирует расходы, а сигналищзирует о том, что в системе появились новые платные ресурсы

![alt text](/lab02/photos/image-6.png)

все готово!
____________________________________________________

#### Задание 3. Создание и запуск EC2 экземпляра (виртуальной машины)

> задание

разобьем на части.

#### 1.

- Откройте сервис EC2.
- В меню слева выберите Instances → Launch instances.
- Заполните соответствующие параметры для запуска виртуальной машины:

    - Name and tags: webserver.
    - AMI: Выберите Amazon Linux 2023 AMI. Это образ, который будет использоваться для создания виртуальной машины.
    - Instance type: t3.micro.
    - Key pair. Это криптографическая пара ключей (приватный и публичный). Она нужна для безопасного входа на сервер по SSH.
    - Выберите "Create a new key pair".
    - Введите имя для ключа в формате yournickname-keypair.
    - Нажмите "Create key pair" и скачайте файл с приватным ключом (расширение .pem). Сохраните его в надежном месте.
    - Security group. Это набор правил, которые определяют, какой трафик разрешен к вашему экземпляру.

        - Выберите "Create a new security group".
        - Введите имя группы webserver-sg.
        - Добавьте два правила для входящего трафика (Inbound rules).
        - Разрешить входящий HTTP трафик с любого IP-адреса.
        - Разрешить входящий SSH трафик только с вашего текущего IP-адреса.

**EC2** - сервис, которые предоставляет масштарбируемые виртуальныве серверы в облаке, на которых можно запускать свои приложения

![alt text](/lab02/photos/image-7.png)

на фото выше представлена следующая настройка: имя инстанса, ОС, тип инстанса (t3 тип, имеющий 2 центральных процессора и 1 гб памяти)

![alt text](/lab02/photos/image-8.png)

создаем пару ключей для безопасного подключения к сервису по SSH

пара ключй состоит из приватного и публичного. публичный - позволяет провести идентификацию пользщователей, которым разрешено подключение. приватный - позволяет доказать серверу что это именно мы. т.е публичный ключ может юыть доступен всем, а приватный только у одного пользователя. так же могу подметить, что публичный ключ не дает доступ сам по себе, а ннеобходим для проверки приватного ключа

![alt text](/lab02/photos/image-9.png)

установил приватный ключ

далее необходимо установить разрешенный трафик

![alt text](/lab02/photos/image-10.png)

по заданию мы должны разрешить http трафик с любого IP адреса, а подключение по shh только с IP устройства.

#### 2

> задание

- Network settings. Оставьте настройки по умолчанию. AWS автоматически создаст виртуальную сеть (VPC) и подсеть (subnet).

- Configure Storage. Оставьте настройки по умолчанию.

- Пролистайте вниз до Advanced details → User Data и вставьте следующий скрипт:

    - #!/bin/bash
    dnf -y update
    dnf -y install htop 
    dnf -y install nginx
    systemctl enable nginx
    systemctl start nginx


> Что такое User Data и какую роль выполняет данный скрипт? Для чего используется nginx?

- Нажмите Launch instance и дождитесь статуса Running и Status checks: 2/2. После того, как виртуальная машина запустится, вы увидите её публичный IP-адрес в колонке "IPv4 Public IP".

- Проверьте, что веб-сервер работает, открыв в браузере URL: http://<Public-IP>, где <Public-IP> — это публичный IP-адрес вашей виртуальной машины.

> выполнение

настройки сетей и хранилища оставляем нетронутыми и переходим к настройке `User Data`

**User Data** - является набором утилит, которые выполнятся при первом запуске виртуальной машины.

скрипт из описания работы:

```bash
#!/bin/bash
dnf -y update # 1
dnf -y install htop # 2
dnf -y install nginx # 3
systemctl enable nginx # 4
systemctl start nginx # 5
```

1) тут обновляются все пакеты на сервере до последжних версий
2) устанавливается инструмент htop для мониторинга ресурсов сервера
3) устанавливается nginx-веб-сервер
4) настраивается автоматический запуск nginx при старте сервера
5) запускается nginx для обработки http запросов

> Что такое User Data и какую роль выполняет данный скрипт? Для чего используется nginx?

**User Data** - является набором утилит, которые выполнятся при первом запуске виртуальной машины. данный скрипт обновляет пакеты, устанавливает утилиты (htop, nginx). nginx - ПО, который работает как посредлник между нашим браузером и веб сайтом. браузер отправляет запрос на сервер; nginx получает этот запрос и определяет, как его обработать(отправка статического файла или пересылка запроса на лдругой сервер с возвратом ответа). 

![alt text](/lab02/photos/image-11.png)

запускаем инстанс

![alt text](/lab02/photos/image-13.png)

видим инит сообзение от nginx, все работает

![alt text](/lab02/photos/image-14.png)

____________________________________________________

#### Задание 4. Логирование и мониторинг

> задание: 

- Откройте вкладку Status checks.
    - В карточке вашего инстанса EC2 найдите вкладку Status checks.
Здесь вы можете быстро определить, выявил ли Amazon EC2 какие-либо проблемы, которые могут помешать работе приложений.
Amazon EC2 выполняет автоматические проверки для каждого работающего экземпляра:
- System reachability check — проверяет инфраструктуру AWS (железо и гипервизор).
- Instance reachability check — проверяет, доступна ли операционная система на уровне инстанса.
Убедитесь, что обе проверки прошли успешно (2/2 checks passed).
______

- Откройте вкладку Monitoring.
На этой вкладке отображаются метрики Amazon CloudWatch для вашего инстанса.
Так как инстанс был создан недавно, метрик пока немного.
Вы можете нажать на иконку с тремя точками на любом графике и выбрать Enlarge, чтобы открыть метрику в развёрнутом виде.
По умолчанию включён базовый мониторинг (Basic monitoring) — данные отправляются в CloudWatch каждые 5 минут.
При необходимости можно включить детализированный мониторинг (Detailed monitoring) — метрики будут отправляться каждую минуту.

> В каких случаях важно включать детализированный мониторинг?
_____
- Просмотр системного лога (System Log)
- В верхнем меню нажмите Actions → Monitor and troubleshoot → Get system log.
- Здесь отображается вывод консоли инстанса. Это полезный инструмент для диагностики:
    - помогает разбирать ошибки ядра,
    - ошибки конфигурации сервисов,
    - проблемы, из-за которых инстанс может завершиться или стать недоступным до старта SSH.
- Пролистайте вывод и найдите строки, показывающие установку пакетов (например, nginx из вашего User Data).
Если лог пока пуст — подождите пару минут и попробуйте снова.
Нажмите Cancel, чтобы выйти.
_____
- Просмотр снимка экрана инстанса (Instance Screenshot)
В меню выберите Actions → Monitor and troubleshoot → Get instance screenshot.
Вы увидите изображение консоли вашего EC2 (как если бы к нему был подключён монитор).
Это особенно полезно, если вы не можете подключиться к инстансу по SSH: скриншот помогает понять, зависла ли ОС, есть ли kernel panic или другие ошибки.
Нажмите Cancel, чтобы выйти.
_____

> выполнение

![alt text](/lab02/photos/image-15.png)

тесты пройдены, все в норме


![alt text](/lab02/photos/image-16.png)

экран с метриками, где данные по умолчанию обновляются каждые 5 минут,  `пример`:

![alt text](/lab02/photos/image17.png)

> В каких случаях важно включать детализированный мониторинг?

детализированные мониторинг важно включать, если сервис может быть чувствителен к сбоям из-за высокой нагрузки, чтобы быстрее обнаружить и реагировать на такие сбои.

так же при авто масштарбировании необходимое точное измерение метрик, чтобы оно не срабатывало с ззадержкой, и не приводило к перегрузке и простою

иногда детализированный мониторинг нужен для оптимизации затрат, если необходимо точно понимать нагрузку и использовать ресурсы максимально эффективно

![alt text](/lab02/photos/image18.png)

один из выводов лог консоли инстанса представлен выше, тут виден начальный процесс установки nginx и доп пакетов (основной код, структуру каталогов и тд)

____________________________________________________

#### Задание 5. Подключение к EC2 инстансу по SSH

> задание:

Откройте терминал на вашем компьютере.
Перейдите в директорию, где сохранён файл приватного ключа .pem.

cd /path/to/your/key
Установите права на ключ (для Linux/MacOS):

chmod 400 yournickname-keypair.pem
Если используете PowerShell или CMD, убедитесь, что:

Файл ключа .pem находится в директории, доступной только вашему пользователю,ине имеет разрешений для других пользователей.

Настроить можно следующим образом:

Щёлкните правой кнопкой мыши на файле .pem и выберите "Свойства".
Перейдите на вкладку "Безопасность".
Убедитесь, что доступ есть только у вашей учётной записи Windows.
Удалите права у «Все» (Everyone) или других пользователей.
Подключитесь к инстансу по SSH:

ssh -i yournickname-keypair.pem ec2-user@<Public-IP>
где,

-i — это параметр, указывающий на файл приватного ключа.
yournickname-keypair.pem — это имя вашего файла с приватным ключом.
ec2-user — это стандартное имя пользователя для Amazon Linux AMI.
<Public-IP> — это публичный IP-адрес инстанса EC2.
После успешного подключения вы должны увидеть приглашение командной строки, например:

[ec2-user@ip-xx-xx-xx-xx ~]$
Выполните команду для проверки статуса веб-сервера Nginx:

systemctl status nginx
Почему в AWS нельзя использовать пароль для входа по SSH?

Далее, в зависимости от вашей специализации, выберите одно из трёх заданий (6a, 6b или 6c). Для специализации Frontend & Backend & Security подойдут задания 6a и 6b, для специализации DevOps — рекомендуется задание 6c.

> выполнение:

устанавливаем права на ключ:

```bash
Ryzen:/mnt/host/d/shh-keys# ls -l
total 4
-rwxrwxrwx    1 root     root          1678 Oct 12 13:02 user_admin-keypair.pem
-rwxrwxrwx    1 root     root           128 Oct 12 10:49 user_admin_example_credentials.csv
Ryzen:/mnt/host/d/shh-keys# chmod 400 user_admin-keypair.pem
Ryzen:/mnt/host/d/shh-keys# ls -l
total 4
-r--------    1 root     root          1678 Oct 12 13:02 user_admin-keypair.pem
-rwxrwxrwx    1 root     root           128 Oct 12 10:49 user_admin_example_credentials.csv
Ryzen:/mnt/host/d/shh-keys#
```

владелей файла имеет право на чтение, остальные - нет

![alt text](/lab02/photos/image19.png)

благодаря утилити puttygen конвертируе pem вфайл в ppk

![alt text](/lab02/photos/image20.png)

успешное подключение

```bash
[ec2-user@ip-172-31-20-126 ~]$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
     Active: active (running) since Sun 2025-10-12 13:34:38 UTC; 1h 52min ago
    Process: 5416 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 5499 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 5553 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 5610 (nginx)
      Tasks: 3 (limit: 1053)
     Memory: 3.3M
        CPU: 78ms
     CGroup: /system.slice/nginx.service
             ├─5610 "nginx: master process /usr/sbin/nginx"
             ├─5611 "nginx: worker process"
             └─5612 "nginx: worker process"
```

> Почему в AWS нельзя использовать пароль для входа по SSH?

AWS не разрешает вход по паролю для SSH из-за высокого риска взлома пароля пользователей: к примеру интернет полон ботов, которые пытаются подобрать популярные пароли на портах 22

ssh ключи намного безопаснее, к примеру приватный ключ находится только у самого пользователя, без него никто не сможет зайти войти в систему. плюс при работе с большим кол-вом серверов проще раздавать ключи, нежели управлять паролями

__________

развертывание веб приложение (spring boot)

моя идея: приложение в котором nginx выступает в роли прокси между браузером и беком на спринге(взаимодействие с внешним API) ...

пример одного из эндпоинтов:

класс описывающий ответ на запрос получения

```java
package simpsons_api_test.simpsons_api.responses;

import com.fasterxml.jackson.annotation.JsonProperty;
import java.util.List;

public class CharacterResponse {

    private int id;
    private int age;

    @JsonProperty("birthdate")
    private String birthdate;

    private String name;
    private String occupation;
    private String status;

    @JsonProperty("phrases")
    private List<String> phrases;

    @JsonProperty("portrait_path")
    private String portraitPath;
```

технология webclient, которая предоставляет доступ к внешним API, 

метод `Mono<CharacterResponse> getCharacterFromApi` возвращает как минимум один ответ с типом `CharacterResponse`, принимающим id персонажа в качестве аргумента

```java
@Service
public class ApiService {

    private final WebClient webClient;

    public ApiService(WebClient.Builder webClient) {
        this.webClient = webClient.baseUrl("https://thesimpsonsapi.com/api").build();
    }

    public Mono<CharacterResponse> getCharacterFromApi(int id) {
        return webClient
                .get()
                .uri("/characters/{id}", id)
                .retrieve()
                .bodyToMono(CharacterResponse.class);
    }
```

контроллер просто инкапсулирует работу запроса и возвращает json ответ

```java

 @RestController
public class Controller {
    private final ApiService apiService;

    public Controller(ApiService apiService) {
        this.apiService = apiService;
    }

    @GetMapping("/characters/{id}")
    public Mono<CharacterResponse> getCharacter(@PathVariable int id) {
        return apiService.getCharacterFromApi(id);
    }

```

_______

вторым этапом является сборка файла в один `jar` файл хранящий всю конфигурацию, библиотеки и код проекта

![alt text](/lab02/photos/image22.png)

далее необходимо перенести jar файл на ec сервер

```bash
PS C:\Users\Calculator\Desktop\app\simpsons_api> .\pscp.exe -i D:\shh-keys\user_admin-keypair.ppk .\target\simpsons_api-0.0.1-SNAPSHOT.jar ec2-user@18.208.142.136:/home/ec2-user/
simpsons_api-0.0.1-SNAPSH | 23639 kB | 7879.9 kB/s | ETA: 00:00:00 | 100%
PS C:\Users\Calculator\Desktop\app\simpsons_api>
```

файл был скопирован в дом директорию пользователя при помощи технологии `pscp`


```bash
Using username "ec2-user".
Authenticating with public key "imported-openssh-key"
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sun Oct 12 17:28:08 2025 from 178.168.75.101
[ec2-user@ip-172-31-20-126 ~]$ ls
simpsons_api-0.0.1-SNAPSHOT.jar 
[ec2-user@ip-172-31-20-126 ~]$
```

все присутсвует, едем дальше

необходимо создать свой сервис для автозапуска приложения при старте (чтобы в ручную не запускать постоянно jar файл)

```bash
[Unit]
Description=Simpsons API
After=network.target // запуск сервиса после подключения к сети (иначе спринг просто упадет)

[Service]
User=ec2-user // сервис от нашего имени
ExecStart=/usr/bin/java -jar /home/ec2-user/simpsons_api-0.00.1-SNAPSHOT.jar // команда запуска
Restart=always // перезапуск если падаем
RestartSec=10 // перед перезапуском ждем 10 секунд

[Install]
WantedBy=multi-user.target // по гайду
```

осталось только настроить `nginx`

```bash

server {
listen 80; # слушаем 80 порт
server_name _;
location / { # правила для всех запросов
proxy_pass http://127.0.0.1:8080; # перенаправление запроса на локальный спринг сервер
proxy_set_header Host $host; # по гайду для принятия реального домена
proxy_set_header X-Real-IP $remote_addr; # по гайду для того, чтобы сервис видел от кого был запрос
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # по гайду добавления цепочки из IP клиентов для анализа
}
}
```

перезапускаем nginx и проверяем результаты:


> перс по id 

```java
    public Mono<CharacterResponse> getCharacterFromApi(int id) {
        return webClient
                .get()
                .uri("/characters/{id}", id)
                .retrieve()
                .bodyToMono(CharacterResponse.class);
    }
```

![alt text](/lab02/photos/image23.png)


> рандомные 3 персa

```java
    public Mono<List<CharacterResponse>> getRandomCharacters() {
        Random random = new Random();
        return Flux.range(random.nextInt(1, 50), 3).flatMap(this::getCharacterFromApi).collectList();
    }
```

![alt text](/lab02/photos/image24.png)

> эпизод по id

```java
// формат ответа

public class EpisodeResponse {

    @JsonProperty("id")
    private int id;

    public String airdate;

    @JsonProperty("episode_number")
    private int episodeNumber;

    @JsonProperty("name")
    private String name;

    @JsonProperty("season")
    private int season;

    @JsonProperty("synopsis")
    private String synopsis;

```

запрос 


```java

    public Mono<EpisodeResponse> getEpisode(int id){
        return webClient.get()
                .uri("/episodes/{id}", id)
                .retrieve()
                .bodyToMono(EpisodeResponse.class);
    }
```

![alt text](/lab02/photos/image25.png)

________________________

> Чем «Stop» отличается от «Terminate»

STOP - останавливает инстанс, вм остается, однако не работает

TERMINATE - унгичтожить, инстанс просто удаляется. восстановить данные - невозможно

________

далее хочется попробовать поработать над разработкой SPA для этой лабораторной работы, значит коммиты еще могут быть

______

ИСТОЧНИКИ: 

[VIDOS](https://www.youtube.com/watch?v=KZFkssRuBLE)

