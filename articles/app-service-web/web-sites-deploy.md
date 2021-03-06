<properties
	pageTitle="Развертывание приложения в службе приложений Azure"
	description="Узнайте, как разворачивать приложение в службе приложений Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Развертывание приложения в службе приложений Azure

Эта статья поможет вам определить лучший способ развертывания файлов веб-приложения, серверной части мобильного приложения или приложения API в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Здесь также приведены ссылки на соответствующие статьи и записи в блогах, в которых содержатся пошаговые инструкции, посвященные требуемому способу.

## <a name="overview"></a>Обзор способов развертывания

Сразу после создания или подготовки приложения в службе приложений Azure и перед развертыванием в нем кода служба приложений Azure автоматически начнет обслуживать платформу приложений (ASP.NET, PHP, Node.js и т. д.). Некоторые платформы активированы по умолчанию, в то время как для активации других, например Java и Python, может потребоваться небольшая настройка параметров. Кроме того, можно изменить настройки платформы приложения, например версию PHP или разрядность среды выполнения. Дополнительные сведения см. в статье [Настройка приложения в службе приложений Azure](web-sites-configure.md).

Так как о веб-сервере и платформе приложений можно не беспокоиться, развертывание приложения в службе приложений сводится к развертыванию кода, двоичных файлов, файлов содержимого и соответствующей им структуры каталогов [в Azure в каталог **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (или в каталог веб-заданий **/Data/Jobs**). Служба приложений поддерживает три способа развертывания, которые описаны ниже.

- [С использованием FTP или FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): используйте удобный для вас инструмент с поддержкой FTP или FTPS для перемещения файлов в Azure, из [FileZilla](https://filezilla-project.org) в полнофункциональные IDE, например [NetBeans](https://netbeans.org). Таким образом выполняется только процесс отправки файлов. В службе приложений не предоставляются дополнительные функции, такие как управление версиями, структурой файлов и т. д. 
- [С использованием Kudu (Git или Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): это [обработчик развертывания](https://github.com/projectkudu/kudu/wiki) в службе приложений. Код можно отправить в Kudu непосредственно из любого репозитория. При отправке кода в Kudu предоставляются дополнительные функции, в том числе управление версиями, восстановление пакета, MSBuild и [веб-привязки](https://github.com/projectkudu/kudu/wiki/Web-hooks), для непрерывного развертывания и выполнения других задач автоматизации. Все эти функции настраиваемые и могут активироваться в следующих случаях: 
    - каждый раз, когда **git push** выполняется из настроенного репозитория Git;
	- каждый раз, когда **hg push** выполняется из настроенного репозитория Mercurial; 
    - каждый раз, когда связанное облачное хранилище, например Dropbox или OneDrive, синхронизируется со службой приложений. 
- [Использование веб-развертывания](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): тот же инструментарий, который автоматизирует развертывание на серверах IIS. Код можно развернуть в службе приложений непосредственно из привычных инструментов Microsoft, например Visual Studio, WebMatrix и Visual Studio Team Services. Этот инструмент поддерживает развертывание по методу diff-only, создание баз данных, преобразование строк подключения и т. д. Использование веб-развертывания и Kudu отличается тем, что двоичные файлы приложения создаются перед их развертыванием в Azure. Как и в случае с FTP, дополнительные функции в службе приложений не предоставляются.

Распространенные инструменты веб-развертывания поддерживают один или несколько из этих способов развертывания. В то время как выбранный инструмент определяет, какие способы развертывания можно использовать, функции DevOps, находящиеся в вашем распоряжении, зависят от сочетания способа развертывания и конкретных инструментов. Например, при выполнении веб-развертывания из [Visual Studio с пакетом SDK для Azure](#vspros), несмотря на отсутствие автоматизации Kudu, вам будут доступны восстановление пакетов и автоматизация MSBuild. В пакете SDK для Azure также содержится простой в использовании мастер, который поможет создать необходимые ресурсы Azure непосредственно в рамках интерфейса Visual Studio.

>[AZURE.NOTE] При использовании этих способов развертывания [ресурсы Azure](resource-group-portal), которые могут понадобиться для приложения, например план службы приложений, приложение службы приложений и база данных SQL, на самом деле не подготавливаются. Однако в большинстве связанных статей с инструкциями показан комплексный процесс подготовки приложения и развертывания в нем кода. Дополнительные способы подготовки ресурсов Azure см. в разделе [Автоматизация с помощью программ командной строки](#automate).

## <a name="ftp"></a>Развертывание путем копирования файлов в Azure вручную
Если вы привыкли вручную копировать веб-содержимое для поставщиков услуг веб-размещения, что является обычным рабочим процессов для разработчиков PHP, для копирования файлов можно использовать служебную программу [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol), например Windows Explorer или [FileZilla](https://filezilla-project.org/).

При копировании файлов вручную для развертывания используется протокол FTP (см. раздел [Обзор способов развертывания](#overview)).

Копирование файлов вручную имеет следующие преимущества:

- Привычный инструментарий FTP. 
- Осведомленность о конечном расположении файлов.
- Дополнительная защита благодаря использованию FTPS.
- Это решение для веб-развертывания позволяет использовать самые простые инструменты (например, для разработки веб-приложений можно использовать программу Блокнот).

Копирование файлов вручную имеет следующие недостатки:

- Ответственность за развертывание файлов в правильных каталогах службы приложений.
- При откате в случае возникновения ошибок отсутствует возможность управления версиями.
- Многие инструменты FTP не предоставляют возможности копирования по методу diff-only и просто копируют все файлы. В результате при работе с большими приложениями время развертывания значительно увеличивается даже при наличии незначительных обновлений.

### <a name="howtoftp"></a>Пошаговая инструкция по развертыванию путем копирования файлов в Azure вручную
Копирование файлов в Azure включает в себя несколько простых шагов.

1. Создайте учетные данные развертывания для своего приложения на [портале Azure](https://portal.azure.com). Для этого выберите в колонке своего приложения **Настройка** > **Учетные данные развертывания**.
2. После настройки учетных данных развертывания получите сведения о подключении FTP, выбрав **Параметры** > **Свойства** и скопировав значения параметров **Пользователь разработки и FTP**, **Имя узла FTP** и **Имя узла FTPS**.
3. Подключитесь к приложению из клиента FTP, используя полученные сведения.
4. Скопируйте файлы и соответствующую им структуру каталогов [ в Azure в каталог **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (или в каталог веб-заданий **/Data/Jobs**).
5. Перейдите по URL-адресу приложения, чтобы убедиться, что приложение работает правильно. 

Для получения дополнительных сведений см. следующие ресурсы:

* [Создание веб-приложения PHP-MySQL и его развертывание с помощью FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Использование пакетных скриптов FTP](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>Развертывание путем синхронизации с папкой в облаке
Хорошая альтернатива [копированию файлов вручную](#ftp) — синхронизация файлов и папок со службой приложений из облачной службы хранения, например OneDrive и Dropbox. На [портале Azure](https://portal.azure.com) в облачном хранилище можно настроить специальную папку. Обрабатывайте в ней код и содержимое приложения, а затем синхронизируйте ее со службой приложений нажатием одной кнопки.

При синхронизации с папкой в облаке для развертывания применяется способ с использованием Kudu (см. раздел [Обзор способов развертывания](#overview)).

Развертывание путем синхронизации с папкой в облаке имеет следующие преимущества:

- Простота развертывания. Такие службы, как OneDrive и Dropbox, предоставляют клиенты синхронизации рабочих столов, благодаря чему локальный рабочий каталог становится также каталогом развертывания.
- Развертывание одним щелчком.
- В Kudu доступны все функции (например, управление версиями развертывания, откат, восстановление пакетов, автоматизация).
- Это решение для веб-развертывания позволяет использовать самые простые инструменты.

Развертывание путем синхронизации с папкой в облаке имеет следующие недостатки:

- Это не лучшее решение для командного проекта.

### <a name="howtodropbox"></a>Пошаговая инструкция по развертыванию путем синхронизации с папкой в облаке
 
* [Развертывание в веб-приложения из Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Использование [портала Azure](https://portal.azure.com) для настройки развертывания с помощью Dropbox.
* [Развертывание контента из Dropbox в веб-приложения](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). В этом видео показан пошаговый процесс подключения папки DropBox к веб-приложению и продемонстрировано, как быстро можно активировать веб-приложение или поддерживать его, используя простое развертывание путем перетаскивания.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

## Развертывание с помощью IDE
Если вы уже используете [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) с [пакетом SDK для Azure](https://azure.microsoft.com/downloads/) либо WebMatrix или другим средством IDE, например [Xcode](https://developer.apple.com/xcode/) и [Eclipse](https://www.eclipse.org), развертывание в Azure можно провести непосредственно из IDE. Этот вариант идеально подходит для частных разработчиков.

Visual Studio поддерживает все три способа развертывания (с использованием FTP, Git и веб-развертывания) в зависимости от ваших предпочтений. В свою очередь другие среды IDE можно развернуть в службе приложений, если в них предусмотрена интеграция с FTP или Git (см. раздел [Обзор способов развертывания](#overview)).

Развертывание с помощью IDE имеет следующие преимущества:

- Вероятное сокращение инструментария, требуемого на протяжении жизненного цикла комплексного приложения. Разработка, отладка, отслеживание и развертывание приложения в Azure в пределах IDE. 

Развертывание с помощью IDE отличается следующими недостатками:

- Повышенная сложность инструментария.
- Для командных проектов все равно требуется система управления версиями.

<a name="vspros"></a> Развертывание при помощи Visual Studio с пакетом SDK для Azure имеет следующие дополнительные преимущества:

- Благодаря пакету SDK для Azure ресурсы Azure получают наивысший статус в Visual Studio. Предоставляются возможности создания, удаления, редактирования, запуска и остановки приложений, а также запроса серверной базы данных SQL, отладки приложений Azure в реальном времени и многое другое. 
- Редактирование кода в режиме реального времени в Azure.
- Отладка приложений в режиме реального времени в Azure.
- Интегрированный обозреватель Azure.
- Развертывание по методу diff-only. 

###<a name="vs"></a>Как выполнить развертывание напрямую из Visual Studio

* [Начало работы с Azure и ASP.NET](web-sites-dotnet-get-started.md). Создание и развертывание простого веб-проекта ASP.NET MVC с помощью Visual Studio и веб-развертывания.
* [Развертывание веб-заданий Azure с помощью Visual Studio](websites-dotnet-deploy-webjobs.md). Как настроить проекты консольных приложений для их развертывания как заданий WebJob.  
* [Развертывание безопасного приложения ASP.NET MVC 5 с членством, OAuth и базой данных SQL в веб-приложения](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Создание и развертывание веб-проекта ASP.NET MVC с базой данных SQL с помощью Visual Studio, веб-развертывания и Entity Framework Code First Migrations.
* [Обзор веб-развертывания для Visual Studio и ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Основные сведения о веб-развертывании с помощью Visual Studio. Устарели, но включают сведения, которые могут представлять интерес, в том числе обзор параметров для развертывания базы данных вместе с веб-приложением и список дополнительных задач развертывания, которые вам может потребоваться выполнить или вручную настроить их выполнение в Visual Studio. Этот раздел посвящен развертыванию в целом, а не только развертыванию в веб-приложения.
* [Веб-развертывание ASP.NET с помощью Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Серия учебников из 12 частей, в которой рассматривается более полный список задач развертывания, чем в других ресурсах из этого списка. Некоторые функции развертывания Azure были добавлены уже после написания учебника, но отсутствующие данные приведены в примечаниях, добавленных позже.
* [Развертывание веб-сайта ASP.NET в Azure с помощью Visual Studio 2012 непосредственно из репозитория Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Описание способов развертывания веб-проекта ASP.NET в Visual Studio с использованием подключаемого модуля Git для применения кода к Git и подключения Azure к репозиторию Git. При запуске Visual Studio 2013 поддержка Git доступна по умолчанию и не требует установки подключаемого модуля.

###<a name="webmatrix"></a>Как выполнить развертывание напрямую из WebMatrix

* [Создание и развертывание веб-сайта Node.js в Azure с использованием WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [WebMatrix 3: интеграция Git и развертывание в Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Использование WebMatrix для развертывания из репозитория Git системы управления версиями.

## <a name="onprem"></a>Развертывание из локальной системы управления версиями

При работе в команде разработчиков любого состава и использовании локальной системы управления исходным кодом (SCM), например [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) или [Mercurial](http://mercurial.selenic.com/), в рабочем процессе управления версиями можно настроить службу приложений для интеграции с репозиторием и развертывания непосредственно в этой службе. Кроме того, при использовании TFS можно настроить непрерывное развертывание в службе приложений.

TFS использует веб-развертывание для развертывания в службе приложений, в то время как для развертывания из репозиториев Git и Mercurial используется Kudu (см. раздел [Обзор способов развертывания](#overview)).

Развертывание из локальной системы управления версиями имеет следующие преимущества:

- Поддержка развертывания из любой языковой платформы, а также из клиента Git или Mercurial, в том числе из [Xcode](https://developer.apple.com/xcode/) и [Eclipse](https://www.eclipse.org).
- Развертывание зависит от ветви. Различные версии можно развертывать в отдельных [слотах](web-sites-staged-publishing).
- Хорошо подходит для команд разработчиков любого состава.

Развертывание из локальной системы управления версиями имеет следующие недостатки:

- Требуется знание выбранной системы SCM.
- Может предоставить слишком много функций и возможностей.
- Нехватка готовых решений для непрерывного развертывания и зависящего от ветви развертывания из клиентских средств Git и Mercurial. 

Развертывание с помощью TFS имеет следующие дополнительные преимущества:

- Непрерывная интеграция для сборки, тестирования и развертывания.
- Встроенные средства для совместной работы, которые работают в существующей IDE или редакторе.
- Поддержка Git для системы распределенного управления версиями или системы управления версиями Team Foundation для централизованного управления версиями. 
- Расширенные средства, обеспечивающие гибкое развертывание.
- Готовые интеграции для [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [служебной шины Azure](/services/service-bus/) и многого другого. 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) является бесплатным для группы составом до 5 разработчиков.

###<a name="tfs"></a>Как выполнить непрерывное развертывание с помощью TFS

* [Непрерывная доставка для облачных служб в Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md). Этот документ предназначен для облачной службы Azure, но некоторое его содержимое относится к веб-приложениям.

###<a name="gitmercurial"></a>Как выполнить развертывание из локального репозитория Git или Mercurial

* [Публикация из системы управления версиями в веб-приложения с помощью Git](web-sites-publish-source-control.md). Использование Git для выполнения публикации непосредственно с локального компьютера в веб-приложение (в Azure такой способ публикации называется "локальный репозиторий Git"). Здесь также показано, как включить непрерывное развертывание репозиториев Git из GitHub, CodePlex или BitBucket.
* [Публикация в веб-приложения из любого репозитория git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Блог, который дает пояснения по компоненту "Внешний репозиторий" в веб-приложениях.
* [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* [Развертывание ДВУХ веб-сайтов в Azure из одного репозитория Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Запись в блоге Скотта Хансельмана (Scott Hanselman).

## Развертывание из облачной службы управления версиями
При работе в команде разработчиков любого состава и использовании облачной службы управления исходным кодом, например [Visual Studio Team Services](http://www.visualstudio.com/) (прежнее название — Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) и [Kiln](https://www.fogcreek.com/kiln/), службу приложений можно настроить для интеграции с репозиторием и непрерывного развертывания.

Visual Studio Team Services использует веб-развертывание для развертывания в службе приложений, в то время как для развертывания из сетевых репозиториев используется Kudu (см. раздел [Обзор способов развертывания](#overview)).

Развертывание из облачной службы управления версиями имеет следующие преимущества:

- Поддержка любой языковой платформы.
- Непрерывное развертывание для репозиториев Git и Mercurial, которое не требует настройки. 
- Развертывание зависит от ветви. Различные ветви можно развертывать в различных [слотах](web-sites-staged-publishing).
- Хорошо подходит для команд разработчиков любого состава.

Развертывание из облачной службы управления версиями имеет следующие недостатки:

- Требуется знание выбранной службы SCM.
- Может предоставить слишком много функций и возможностей.

Развертывание при помощи Visual Studio Team Services имеет следующие дополнительные преимущества:

- Бесплатное использование для группы составом до 5 разработчиков.
- Непрерывная интеграция для сборки, тестирования и развертывания.
- Встроенные средства для совместной работы, которые работают в существующей IDE или редакторе.
- Поддержка Git для системы распределенного управления версиями или системы управления версиями Team Foundation для централизованного управления версиями. 
- Расширенные средства, обеспечивающие гибкое развертывание.
- Готовые интеграции для [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [служебной шины Azure](/services/service-bus/) и многого другого. 
- Многофункциональные панели мониторинга, упрощающие создание отчетов благодаря [подключению к Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs).

###<a name="vsts"></a>Как выполнять непрерывное развертывание с помощью Visual Studio Team Services

- [Непрерывная доставка в Azure с использованием Visual Studio Team Services и TFVC](../cloud-services/cloud-services-continuous-delivery-use-vso.md). Пошаговое руководство, в котором показано, как настроить непрерывную доставку из Visual Studio Team Services в веб-приложение, используя TFVC. 
- [Непрерывная доставка в Azure с использованием Visual Studio Team Services и Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md). Схож с предыдущим учебником, но использует Git вместо TFVC.

###<a name="cloudgitmercurial"></a>Как выполнять развертывание из облачного репозитория Git или Mercurial

- [Публикация из системы управления версиями в веб-приложения с помощью Git](web-sites-publish-source-control.md). Пошаговая инструкция по активации непрерывного развертывания репозиториев из GitHub, CodePlex или BitBucket. В этом учебнике показано, как опубликовать репозиторий Git, но для репозиториев Mercurial, размещенных в CodePlex или BitBucket, процесс будет аналогичный.
- [Развертывание в веб-приложения с GitHub с помощью Kudu](https://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Видео, созданное Скоттом Хансельманом (Scott Hanselman) и Дэвидом Эббо (David Ebbo), в котором показан процесс развертывания веб-приложения непосредственно из GitHub в службе приложений.
- [Кнопка "Развернуть в Azure" для веб-приложений](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Блог о методе запуска развертывания из репозитория Git.
- [Форум Azure для Git, Mercurial и DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

Для получения дополнительных сведений см. следующие ресурсы:

- [Публикация из системы управления версиями в веб-приложения с помощью Git](web-sites-publish-source-control.md). Использование Git для выполнения публикации непосредственно с локального компьютера в веб-приложения (в Azure такой способ публикации называется "локальный репозиторий Git"). 

## <a name="automate"></a>Автоматизация развертывания с помощью программ командной строки

* [Автоматизация развертывания с помощью MSBuild](#msbuild)
* [Копирование файлов с помощью средств и сценариев FTP](#ftp)
* [Автоматизация развертывания с помощью Windows PowerShell](#powershell)
* [Автоматизация развертывания с помощью API управления .NET](#api)
* [Развертывание из интерфейса командной строки Azure (Azure CLI)](#cli)
* [Развертывание из командной строки веб-развертывания](#webdeploy)
* [Использование пакетных скриптов FTP](http://support.microsoft.com/kb/96269).
 
Еще один вариант развертывания — использование облачной службы, например [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Дополнительную информацию см. в статье [Развертывание веб-приложений ASP.NET на веб-сайтах Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Автоматизация развертывания с помощью MSBuild

Если для разработки используется [Visual Studio IDE](#vs), то для автоматизации всех действий в IDE можно использовать [MSBuild](http://msbuildbook.com/). Можно настроить MSBuild на использование [веб-развертывания](#webdeploy) или [FTP/FTPS](#ftp) для копирования файлов. Веб-развертывание может также автоматизировать множество других связанных с развертыванием задач, таких как развертывание баз данных.

Для получения дополнительных сведений об использовании командной строки для развертывания с помощью MSBuild см. следующие ресурсы:

* [Веб-развертывание ASP.NET с помощью Visual Studio: развертывание с использованием командной строки](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Десятая часть в серии учебников по развертыванию в Azure с использованием Visual Studio. Здесь показано, как использовать командную строку для развертывания после настройки профилей публикации в Visual Studio.
* [Microsoft Build Engine: использование MSBuild и службы сборки Team Foundation](http://msbuildbook.com/). Печатная копия книги, содержащая главы по использованию MSBuild для развертывания.

###<a name="powershell"></a>Автоматизация развертывания с помощью Windows PowerShell

Можно выполнять функции развертывания с использованием MSBuild или FTP из [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). В этом случае можно также использовать набор командлетов Windows PowerShell, упрощающих вызов API-интерфейса управления Azure REST.

Для получения дополнительных сведений см. следующие ресурсы:

* [Развертывание веб-приложения, связанного с репозиторием GitHub](app-service-web-arm-from-github-provision.md)
* [Подготовка веб-приложения к работе с базой данных SQL](app-service-web-arm-with-sql-database-provision.md)
* [Предсказуемые подготовка и развертывание микрослужб в Azure](app-service-deploy-complex-application-predictably.md)
* [Создание реальных облачных приложений в Azure — полная автоматизация](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Глава электронной книги, в которой объясняется, как пример приложения, показанный в электронной книге, использует скрипты Windows PowerShell для создания тестовой среды Azure и выполнения в ней развертывания. Ссылки на дополнительную документацию по Azure PowerShell см. в разделе [Ресурсы](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Использование скриптов Windows PowerShell для публикации в средах разработки и тестирования](http://msdn.microsoft.com/library/dn642480.aspx). Здесь объясняется, как использовать сценарии развертывания Windows PowerShell, которые создает Visual Studio.

###<a name="api"></a>Автоматизация развертывания с помощью API управления .NET

Вы можете написать на C# код, выполняющий функции MSBuild или FTP для развертывания. В этом случае можно вызвать API-интерфейс управления REST в Azure для выполнения функций управления сайтом.

Для получения дополнительных сведений см. следующий ресурс:

* [Полная автоматизация с использованием библиотек управления Azure и .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Введение в API-интерфейс управления .NET и ссылки на дополнительную документацию.

###<a name="cli"></a>Развертывание из интерфейса командной строки Azure (Azure CLI)

Командную строку на машинах Windows, Mac или Linux можно использовать, чтобы выполнить развертывание с помощью FTP. В этом случае можно также вызвать API управления Azure REST, используя интерфейс командной строки Azure.

Для получения дополнительных сведений см. следующий ресурс:

* [Программы командной строки Azure](/downloads/#cmd-line-tools). Страница портала на сайте Azure.com с информацией о программе командной строки.

###<a name="webdeploy"></a>Развертывание из командной строки веб-развертывания

[Веб-развертывание](http://www.iis.net/downloads/microsoft/web-deploy) — это программное обеспечение корпорации Майкрософт для развертывания в IIS, которое не только предоставляет интеллектуальные функции для синхронизации файлов, но также может выполнять или координировать множество других связанных с развертыванием задач, которые невозможно автоматизировать при использовании FTP. Например, веб-развертывание позволяет развернуть новую базу данных или обновления базы данных вместе с веб-приложением. Веб-развертывание может также минимизировать время, необходимое для обновления существующего сайта, поскольку интеллектуальная функция копирования позволяет копировать только измененные файлы. В Microsoft WebMatrix, Visual Studio, Visual Studio Team Services и Team Foundation Server предусмотрена встроенная поддержка веб-развертывания, но веб-развертывание можно использовать и непосредственно из командной строки для автоматизации развертывания. Команды веб-развертывания открывают множество возможностей, но их освоение может оказаться нелегкой задачей.

Для получения дополнительных сведений см. следующий ресурс:

* [Простые веб-приложения: развертывание](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Блог Дэвида Эббо (David Ebbo) о средстве, которое он разработал, чтобы упростить использование веб-развертывания.
* [Средство веб-развертывания](http://technet.microsoft.com/library/dd568996). Официальная документация на сайте Microsoft TechNet. Устарела, но по-прежнему отлично подходит для начинающих.
* [Использование веб-развертывания](http://www.iis.net/learn/publish/using-web-deploy). Официальная документация на сайте Microsoft IIS.NET. Также устарела, но по-прежнему отлично подходит для начинающих.
* [StackOverflow](http://www.stackoverflow.com). Самое подходящее место для поиска свежей информации об использовании веб-развертывания из командной строки.
* [Веб-развертывание ASP.NET с помощью Visual Studio: развертывание с использованием командной строки](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Подсистема сборки MSBuild используется в Visual Studio и может также использоваться из командной строки для развертывания веб-приложений в веб-приложениях. Этот учебник входит в серию учебных материалов, посвященных главным образом развертыванию в Visual Studio.

##<a name="nextsteps"></a>Дальнейшие действия

В некоторых сценариях вам может понадобиться возможность простого переключения между тестовой или рабочей версиями вашего веб-приложения. Дополнительную информацию см. в разделе [Промежуточное развертывание в веб-приложения](web-sites-staged-publishing.md).

Наличие резервного копирования и плана восстановления является важной частью рабочего процесса развертывания. Информацию о функции резервного копирования и восстановления веб-приложений см. в разделе [Резервное копирование веб-приложений](web-sites-backup.md).

Дополнительную информацию о том, как с помощью средств управления доступом на основе ролей в Azure управлять доступом к развертыванию веб-приложений, см. в статье [RBAC и публикация веб-приложений](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).

Дополнительную информацию по теме развертывания см. в разделе "Развертывание" [документации по веб-приложениям](/documentation/services/web-sites/).

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_0211_2016-->