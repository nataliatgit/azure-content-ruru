<properties 
	pageTitle="Настройка веб-приложений в службе приложений Azure" 
	description="Настройка веб-приложения в службе приложений Azure" 
	services="app-service" 
	documentationCenter="" 
	authors="erikre" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="tdykstra"/>


# Настройка веб-приложений в службе приложений Azure #

В этом разделе рассматривается настройка веб-приложения с помощью [портала Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Параметры приложения

1. На [портале Azure](https://portal.azure.com/) откройте колонку для веб-приложения.
2. Щелкните **Все параметры**.
3. Щелкните **Параметры приложения**.

![](./media/web-sites-configure/configure01.png)

В колонке **Параметры приложения** параметры сгруппированы по нескольким категориям.

### Общие параметры

**Версии Framework**. Укажите эти параметры, если приложение использует какие-либо из этих инфраструктур разработки:

- **.NET Framework**: укажите версию .NET Framework. 
- **PHP**: укажите версию PHP или установите значение **Выключено**, чтобы отключить PHP.
- **Java**: выберите версию Java или задайте **Выключено**, чтобы отключить Java. Используйте параметр **Веб-контейнер**, чтобы выбрать версию Tomcat или Jetty.
- **Python**: выберите версию Python или задайте **Выключено**, чтобы отключить Python.

По техническим причинам включение Java для веб-приложения отключает использование .NET, PHP и Python.

<a name="platform"></a> **Платформа**. Выберите разрядность среды выполнения приложения – 32 или 64 бита. Для 64-битной среды требуется режим "Базовый" или "Стандартный". В 32-разрядной среде режим свободного и общего доступа выполняются всегда.

**Веб-сокеты**. Установите значение **Включено**, чтобы разрешить использование протокола WebSocket. Например, если ваш веб-сайт использует [ASP.NET SignalR](http://www.asp.net/signalr) или [socket.io](web-sites-nodejs-chat-app-socketio.md).

<a name="alwayson"></a> **Всегда включено**. По умолчанию в случае простоя в течение определенного периода времени веб-приложения будут выгружены. Это позволяет сэкономить системные ресурсы. В режиме Basic и Standard вы можете включить функцию **Всегда включено**, чтобы приложение постоянно оставалось загруженным. Если приложение выполняет непрерывные веб-задания, следует включить функцию **Всегда включено**, иначе веб-задания не будут выполняться правильно.

**Версия управляемого конвейера**. Задает [режим конвейера](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application) IIS. Оставьте здесь значение "Интегрированный" (по умолчанию), кроме тех случаев, когда имеется устаревшее веб-приложение, для которого требуется предыдущая версия IIS.

**Автоматическое переключение**. При включении автоматического переключения для слота развертывания служба приложений будет автоматически переводить веб-приложение в рабочий режим после каждого обновления в этом слоте. Дополнительную информацию см. в статье [Развертывание промежуточных слотов для веб-приложений в службе приложения Azure](web-sites-staged-publishing.md).


### Отладка

**Удаленная отладка**. Включение удаленной отладки. Если этот параметр включен, вы можете использовать удаленный отладчик Visual Studio для прямого подключения к веб-приложению. Удаленная отладка останется включенной на 48 часов.


### Параметры приложения

Этот раздел содержит пары "имя и значение", которые веб-приложение будет загружать при запуске.

- Для приложений .NET эти параметры вносятся в конфигурацию .NET `AppSettings` во время выполнения, переопределяя текущие настройки. 

- Во время выполнения приложения PHP, Python, Java и Node могут обращаться к этим параметрам как к переменным среды. Для каждого параметра приложения создаются две переменные среды: одна с именем, указанным в параметре приложения, и другая с префиксом APPSETTING_. Обе содержат одинаковое значение.

### Строки подключения

Строки подключения для связанных ресурсов.

Для приложений .NET эти строки подключения будут внесены в параметры конфигурации .NET `connectionStrings` во время выполнения, переопределяя существующие записи, для которых значение ключа равно имени связанной базы данных.

Для приложений PHP, Python, Java и Node эти параметры будут доступны во время выполнения в виде переменных среды. В качестве префикса будет использоваться тип подключения. Префиксы переменных среды:

- SQL сервер: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- База данных SQL: SQLAZURECONNSTR_
- Нестандартный: CUSTOMCONNSTR_

Например, если строка подключения MySql будет иметь имя `connectionstring1`, доступ к ней будет выполняться через переменную среды `MYSQLCONNSTR_connectionString1`.

### Стандартные документы

Документ по умолчанию — это веб-страница, которая отображается при открытии корневого URL-адреса веб-сайта. Используется первый найденный файл в списке.

Веб-приложения могут использовать модули, для которых выполняется маршрутизация на основе URL-адреса вместо выдачи статичного содержимого. В таком случае документ по умолчанию не используется.

### Сопоставления обработчиков

Добавьте пользовательские обработчики скриптов, которые будут обрабатывать запросы для файлов с определенными расширениями.

- **Расширение**. Расширение обрабатываемого файла, например *.php или handler.fcgi.
- **Путь к обработчику сценариев**. Абсолютный путь к обработчику сценариев. Запросы к файлам, совпадающим с расширением, будут обрабатываться обработчиком сценариев. Используйте путь `D:\home\site\wwwroot` для указания корневого каталога веб-приложения.
- **Дополнительные аргументы**. Необязательные аргументы для командной строки обработчика сценариев. 


### Виртуальные приложения и каталоги 
 
Чтобы настроить виртуальные приложения и каталоги, укажите каждый виртуальный каталог и его соответствующий физический путь относительно корневого каталога веб-сайта. Или установите флажок **Приложение**, чтобы отметить виртуальный каталог как приложение.


## Включение журналов диагностики

Включение журналов диагностики

1. В столбце веб-приложений щелкните **Все параметры**.
2. Щелкните **Журналы диагностики**. 

Параметры записи журналов диагноcтики из веб-приложения, поддерживающего ведение журналов.

- **Ведение журнала приложений**. Эта функция записывает журналы приложений в файловую систему. Ведение журнала продолжается в течение 12 часов. 

**Уровень**. Если включено ведение журналов приложений, этот параметр задает объем данных, который будет записан (ошибка, предупреждение, сведения или подробные сведения).

**Ведение журнала веб-сервера**. Журналы сохраняются в расширенном формате журнала W3C.

**Подробные сообщения об ошибках**. Сохраняет HTM-файлы с подробными сведениями об ошибках. Файлы сохраняются в папке /LogFiles/DetailedErrors.

**Трассировка неудачно завершенных запросов**. Журналы неудачных запросов к XML-файлам. Файлы сохраняются в каталог /LogFiles/W3SVC*xxx*, где xxx – это уникальный идентификатор. Эта папка содержит XSL-файл и один или несколько XML-файлов. Обязательно загрузите XSL-файл, т. к. предоставляет средства форматирования и фильтрации содержимого XML-файлов.

Для просмотра файлов журнала необходимо создать учетные данные FTP, как описано ниже.

1. В столбце веб-приложений щелкните **Все параметры**.
2. Щелкните **Учетные данные развертывания**.
3. Введите имя пользователя и пароль.
4. Щелкните **Сохранить**.

![](./media/web-sites-configure/configure03.png)


Полным именем пользователя FTP будет app\username, где *app* — это имя вашего веб-приложения. Имя пользователя указано в колонке веб-приложения в разделе **Основные сведения**.

![](./media/web-sites-configure/configure02.png)

## Другие задачи по настройке

### SSL 

В стандартном и базовом режиме можно загрузить SSL-сертификаты для настраиваемого домена. Дополнительные сведения см. в статье [Включение протокола HTTPS для веб-приложения](web-sites-configure-ssl-certificate.md).

Чтобы просмотреть загруженные сертификаты, щелкните **Все параметры** > **Личные домены и SSL**.

### Имена доменов

Добавление имени личного домена для веб-приложения. Дополнительную информацию см. в статье [Настройка личного доменного имени для веб-приложения в службе приложений Azure](web-sites-custom-domain-name.md).

Чтобы просмотреть доменные имена, щелкните **Все параметры** > **Личные домены и SSL**.

### Развернутые приложения

- Настройка непрерывного развертывания. См. статью [Использование Git для развертывания веб-приложений в службе приложений Azure](web-sites-publish-source-control.md).
- Слоты развертывания. См. статью [Развертывание в промежуточную среду для веб-приложений в службе приложений Azure](web-sites-staged-publishing.md).

Чтобы просмотреть слоты развертывания, щелкните **Все параметры** > **Слоты развертывания**.


### Мониторинг

В базовом и стандартном режиме можно проверить доступность конечных точек HTTP и HTTPS из трех географических разнесенных мест. Тест мониторинга завершается с ошибкой, если код ответа HTTP говорит об ошибке (4xx или 5xx) или на ответ требуется более 30 секунд. Конечная точка считается доступной, если тесты мониторинга завершились для нее успешно для всех указанных расположений.

Дополнительные сведения см. в статье [Мониторинг состояния конечных точек Интернета](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).

>[AZURE.NOTE] Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Дальнейшие действия

- [Настройка пользовательского имени домена](web-sites-custom-domain-name.md)
- [Включение HTTPS](web-sites-configure-ssl-certificate.md)
- [Масштабирование веб-приложения в службе приложений Azure](web-sites-scale.md)
- [Основы мониторинга для веб-приложений в службе приложений Azure](web-sites-monitor.md)

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_0302_2016-->