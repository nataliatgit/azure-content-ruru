<properties 
   pageTitle="Заметки о выпуске виртуального массива StorSimple | Microsoft Azure"
   description="Здесь описаны критические открытые проблемы виртуального массива StorSimple и способы их устранения."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Заметки о выпуске виртуального массива StorSimple (предварительная версия) 

## Обзор

В приведенных ниже заметках о выпуске описаны критические открытые проблемы общедоступной предварительной версии виртуального массива Microsoft Azure StorSimple (также известного как локальное виртуальное устройство StorSimple или виртуальное устройство StorSimple) от декабря 2015 г.

Заметки о выпуске постоянно обновляются: обнаруживаются и добавляются критические проблемы, требующие временного решения. Перед развертыванием виртуального устройства StorSimple внимательно изучите информацию, содержащуюся в заметках о выпуске.

>[AZURE.IMPORTANT]
>
>- Виртуальный массив StorSimple сейчас находится на этапе предварительной версии и предназначен для оценки и планирования процесса развертывания. Установка этой версии в рабочей среде не поддерживается. 
>
>- В случае возникновения проблем с виртуальным массивом StorSimple оставляйте свои вопросы на [форуме MSDN по StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

В таблице ниже содержится сводка по известным проблемам в этом выпуске.

| Нет.| Функция | Проблема | Временное решение и комментарии |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Обновления | Виртуальные устройства, созданные в предварительной версии, нельзя обновить до поддерживаемой общедоступной версии.| Для таких виртуальных устройств необходимо выполнить отработку отказа с переносом в общедоступную версию, используя процедуру аварийного восстановления.|
| **2.** | Многоуровневые тома или общие папки | Блокировка диапазона байтов для приложений, работающих с многоуровневыми томами StorSimple, не поддерживается. Если блокировка диапазона байтов включена, распределение по уровням StorSimple не будет работать. | Рекомендуемые меры<ul><li>Отключите блокировку диапазона байтов в логике приложения.</li><li> Выберите помещение данных для этого приложения в локально закрепленные тома, а не в многоуровневые тома.</li>* Примечание*. При использовании локально закрепленных томов со включенной блокировкой диапазона байтов помните, что локально закрепленный том может быть включен еще до завершения восстановления. В таких случаях нужно дождаться завершения восстановления.|
| **3.** | Многоуровневые общие папки | Иногда при работе с большими файлами распределение по уровнях выполняется очень медленно. | При работе с файлами большого размера наибольший файл должен занимать менее 3 % от размера общей папки.|
| **4.** | Локально закрепленные тома или общие папки | Используемая емкость локально закрепленного тома или общей папки составляет 85–90 % от выделенного объема. 10–15 % объема зарезервировано для метаданных. | Чтобы получить необходимую используемую емкость, понадобится подготовить том большего размера. Например, для получения 1 ТБ используемой емкости требуется том объемом 1,15 ТБ.|
| **5.** | Локальный пользовательский веб-интерфейс | Если в Internet Explorer включена функция усиленной безопасности, некоторые страницы локального пользовательского веб-интерфейса, такие как **Устранение неполадок** или **Обслуживание**, могут работать неправильно. Кроме этого, на этих страницах могут не работать кнопки. | Отключите функцию усиленной безопасности в Internet Explorer.|
| **6.** | Локальный пользовательский веб-интерфейс | Локализация локального пользовательского веб-интерфейса не поддерживается в этом выпуске. | Эта возможность будет реализована в более позднем выпуске.|
| **7.** | Локальный пользовательский веб-интерфейс | Сетевые интерфейсы виртуальной машины Hyper-V в пользовательском веб-интерфейсе отображаются как интерфейсы со скоростью передачи 10 Гбит/с. | Эта особенность виртуальной машины Hyper-V. Для виртуальных сетевых адаптеров в этой машине всегда отображается скорость 10 Гбит/с.|                                                                                                 
| **8.** | Аварийное восстановление | Аварийное восстановление файлового сервера можно выполнить только в том же домене, в котором находится исходное устройство. Аварийное восстановление на целевое устройство в другом домене не поддерживается в этом выпуске. | Эта возможность будет реализована в более позднем выпуске.|
| **9.**| Используемая емкость общей папки| Потребление общей папки можно наблюдать даже при отсутствии в ней данных. Это связано с тем, что используемая емкость общих папок включает в себя метаданные. | |                                                                                                                                                                                                                                                         
| **10.** | Параметры часового пояса| Если в зарегистрированном устройстве изменился часовой пояс, этого не видно при диагностических тестах, выполняемых через локальный пользовательский веб-интерфейс. | Эта проблема будет решена в более поздней версии.|
| **11.** | Параметры сети | Если статический IP-адрес настроен для сетевого интерфейса через локальный пользовательский веб-интерфейс, IP-адрес меняется. Однако IP-адрес сервера DNS также изменяется на локальный адрес сайта IPv6. | Эта проблема будет решена в более поздней версии.|
| **12.** | Azure PowerShell | В этом выпуске нельзя управлять виртуальным устройством StorSimple с помощью Azure PowerShell. | Виртуальным устройством можно управлять с помощью классического портала Azure и локального пользовательского веб-интерфейса.|
| **13.** | Задания | Ход выполнения заданий отображается не точно. Индикатор выполнения может перепрыгнуть с отметки 0 сразу к 100 %. | Эта проблема будет решена в более поздней версии.|
| **14.** | Подготовленный диск данных | После подготовки диска данных определенного размера и создания соответствующего виртуального устройства StorSimple не следует расширять или уменьшать этот диск. В противном случае это может привести к потере всех данных на локальных уровнях устройства. | |
| **15.** | Справка | Разделы справки будут недоступны.| Вся документация доступна в Центре загрузки Майкрософт и на сайте документации по Azure. |   

<!---HONumber=AcomDC_0121_2016-->