<properties
   pageTitle="Изменение динамического веб-приложения ASP.NET 5, работающего в локальном контейнере Docker, с помощью функций правки и обновления | Microsoft Azure"
   description="Узнайте, как вносить изменения в приложение, выполняемое в локальном контейнере Docker, и обновлять контейнер с помощью функций правки и обновления"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/17/2016"
   ms.author="tarcher" />

# Изменение динамического веб-приложения ASP.NET 5, работающего в локальном контейнере Docker, с помощью функций правки и обновления

## Обзор
Средства Visual Studio для Docker — это удобный способ локальной разработки и тестирования приложения в контейнере Docker, позволяющий не перезапускать контейнер после каждого внесенного в код изменения. В этой статье рассказывается, как запускать веб-приложение ASP.NET 5 в локальном контейнере Docker, вносить необходимые изменения и обновлять браузер для отображения сделанных изменений с помощью функций правки и обновления.

## Предварительные требования
Должны быть установлены следующие средства.

- [Visual Studio 2015 с обновлением 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP.NET и пакет Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)
- [Средства Visual Studio 2015 для Docker](https://aka.ms/DockerToolsForVS)

> [AZURE.NOTE] Если у вас установлена предыдущая версия средств Visual Studio 2015 для Docker, перед установкой последней версии ее необходимо удалить из панели управления.

## Настройка и тестирование клиента Docker 
В этом разделе описывается, как настроить экземпляр машины Docker по умолчанию и проверить его работоспособность.

1. Создайте экземпляр узла Docker по умолчанию, выполнив в командной строке следующую команду.

		docker-machine create --driver virtualbox default
 
1. Убедитесь, что экземпляр по умолчанию настроен и работает, выполнив в командной строке следующую команду. (Должен быть запущен экземпляр с именем default.

		docker-machine ls 
		
	![][0]
 
1. Выберите узел по умолчанию в качестве текущего, выполнив в командной строке следующую команду.

		docker-machine env default

1. Чтобы настроить оболочку, выполните следующую команду.

		FOR /f "tokens=*" %i IN ('docker-machine env default') DO %i

1. Следующая команда должна отображать пустой ответ активных запущенных контейнеров.

		docker ps

	![][1]
 
> [AZURE.NOTE] Каждый раз, когда перезагружается компьютер разработки, необходимо перезапускать локальный узел Docker. Для этого выполните в командной строке следующую команду: `docker-machine start default`

## Изменение приложения, запущенного в локальном контейнере Docker
Средства Visual Studio 2015 для Docker позволяют разработчикам веб-приложений ASP .NET 5 тестировать и запускать приложения в контейнере Docker, вносить изменения в Visual Studio и обновлять браузер для просмотра изменений в приложении, запущенном в контейнере.

1. В меню Visual Studio выберите **Файл -> Создать -> Проект**. 

1. В разделе **Шаблоны** диалогового окна **Новый проект** выберите **Visual C# > Веб-проект**.

1. Выберите **Веб-приложение ASP.NET**.

1. Присвойте новому приложению имя (или оставьте имя по умолчанию).

1. Нажмите кнопку **OK**.

1. В разделе **Шаблоны ASP.NET 5** выберите **Веб-приложение ASP.NET**.

1. Нажмите кнопку **OK**.

1. В обозревателе решений Visual Studio щелкните проект правой кнопкой мыши и выберите команды **Создать > Поддержка Docker**.

	![][2]
 
1. В узле проекта будут созданы следующие файлы:

	![][3]

1. Укажите для конфигурации решения значение `Debug` и нажмите клавишу **&lt;F5>**, чтобы начать тестирование приложения на локальном компьютере.

1. Когда образ контейнера будет построен и запущен в контейнере Docker, консоль PowerShell попытается запустить веб-приложение в вашем браузере по умолчанию. Если вы пользуетесь обозревателем Microsoft Edge, см. раздел [Устранение неполадок](#troubleshooting).

1. Вернитесь в Visual Studio и откройте `Views\Home\Index.cshtml`.

1. Добавьте следующее HTML-содержимое в конец файла и сохраните изменения:

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	Вернитесь в окно браузера и обновите его.

1.	Прокрутите домашнюю страницу вниз — вы увидите, что все изменения применены. Обратите внимание на то, что повторная компиляция сайта занимает несколько секунд, поэтому, если обновления не отобразились сразу, просто обновите браузер еще раз.

##Устранение неполадок 

- **При запуске приложения PowerShell открывается, отображает ошибку и закрывается. Страница браузера не открывается.**

	Возможно, во время `docker-compose-up` возникла ошибка. Чтобы увидеть ошибку, выполните следующие действия:

	1. Откройте файл `Properties\launchSettings.json`.
	
	1. Найдите запись Docker.
	
	1. Найдите строку, которая начинается следующим образом:

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. Добавьте параметр `-noexit`, чтобы строка приняла показанный ниже вид. Таким образом, PowerShell останется открытым, и вы сможете увидеть ошибку.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **Сборка: не удалось создать образ. Ошибка проверки TLS-подключения: узел не работает**

	Убедитесь, что узел Docker по умолчанию работает. См. раздел [Настройка клиента Docker](#configuring-the-docker-client).

- **Не удалось найти сопоставление тома**

	По умолчанию VirtualBox предоставляет совместный доступ к `C:\Users` как к `c:/Users`. Если проект не находится в каталоге `c:\Users`, добавьте его в [общие папки](https://www.virtualbox.org/manual/ch04.html#sharedfolders) VirtualBox вручную.

- **Назначение Microsoft Edge браузером по умолчанию**

	При использовании обозревателя Microsoft Edge сайт может не открываться из-за того, что Edge считает IP-адрес небезопасным. В этом случае выполните следующие действия: 1. В поле "Выполнить" операционной системы Windows введите `Internet Options`. 2. Нажмите появившийся пункт **Свойства браузера**. 2. Откройте вкладку **Безопасность**. 3. Выберите зону **Местная интрасеть**. 4. Нажмите **Узлы**. 5. Добавьте IP-адрес виртуальной машины (в данном случае узел Docker) в список. 6. Обновите страницу в Edge — сайт заработает. 7. Дополнительные сведения об этой проблеме см. в публикации [Microsoft Edge не видит или не открывает локальные веб-сайты в VirtualBox](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) блоге Скотта Хансельмана.

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-ps.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0218_2016-->