<properties 
   pageTitle="Уровень автономной пакетной обработки (шаблоны архитектуры Azure)" 
   description="Шаблон уровня автономной пакетной обработки является частью области инфраструктуры, подробно описанной в документе об архитектуре CPIF." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Уровень автономной пакетной обработки (шаблоны архитектуры Azure)

[Инфраструктура интеграции облачных платформ (CPIF)](azure-architectures-cpif-overview.md) предоставляет инструкции по интеграции рабочей нагрузки для адаптации приложений в облачном решении корпорации Майкрософт.

Инфраструктура интеграции облачных платформ описывает способы, которые следует использовать организациям, клиентам и партнерам для разработки и развертывания предназначенных для облака рабочих нагрузок с помощью гибридной облачной платформы и возможностей управления Azure, System Center и Windows Server.

Шаблон **уровня автономной пакетной обработки** является частью области **инфраструктуры**, подробно описанной в документе об архитектуре CPIF.

##  Уровень автономной пакетной обработки

Шаблон уровня автономной пакетной обработки содержит сведения о компонентах и службах Azure, необходимых для отказоустойчивой и масштабируемой обработки данных с сервера. Эти службы реализуются как рабочие роли в облачных службах Azure, которые в настоящий можно развертывать в любом центре обработки данных Azure.

Рабочие нагрузки пакетной обработки уникальны в том смысле, что обычно они предоставляют пользовательский интерфейс в небольшом объеме или вообще не предоставляют его. Примером такого типа локальной рабочей нагрузки является служба Service, запущенная на Windows Server. Если планируется использовать такой тип рабочей нагрузки в облачной среде, необходимо учитывать, что будет избыточным разворачивать целый сервер для запуска рабочей нагрузки, когда требуется лишь вычисление, хранение и сетевое подключение. Рабочая роль как раз реализует эти операции в Azure.

По определению задание пакетной обработки, выполняемое в Azure, — это рабочая нагрузка, которая подключается к ресурсу, предоставляет некоторую бизнес-логику (вычисления) и выводит данные. Входящие и исходящие ресурсы определяются пользователем и могут быть неструктурированными файлами, BLOB-объектами хранилища Azure, базой данных не-SQL или реляционными базами данных.

Бизнес-логика обычно реализуется в рабочей роли Azure через определение необходимой бизнес-логики в библиотеке .NET. Развертывание рабочей роли — простая операция, однако, чтобы выполнить развертывание рабочей роли, которая будет отказоустойчивой и масштабируемой, требуется разработка, учитывающая способ выполнения службы и ее поддержки в Azure. Этот шаблон будет содержать сведения о разработке, в которой учитываются эти требования и описывается способ их реализации.

## Обзор шаблона архитектуры 

В этом документе описывается шаблон автономной пакетной обработки с использованием экземпляра рабочей роли, находящегося в облачной службе Azure. Ниже приведены важные компоненты для этой разработки. На схеме представлены минимальные необходимые экземпляры для обеспечения отказоустойчивости. Дополнительные экземпляры могут развертываться для повышения производительности службы. Кроме того, можно включить автоматическое масштабирование для масштабирования экземпляров по времени или дополнительным метрикам сервера.

##  Дополнительные ресурсы
[Уровень пакетной обработки (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## См. также
[Архитектура CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Глобальный веб-уровень с балансировкой нагрузки](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Уровень данных с балансировкой нагрузки](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Гибридные сети](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Уровень поиска Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO4-->