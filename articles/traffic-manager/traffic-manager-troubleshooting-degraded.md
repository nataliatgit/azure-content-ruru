<properties
   pageTitle="Устранение неполадок с состоянием ";Деградация"; в диспетчере трафика Azure"
   description="Устранение неполадок с профилями диспетчера трафика при отображении состояния ";Деградация";."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="carmonm"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# Устранение неполадок, связанных со сбоем диспетчера трафика

На этой странице описывается, как устранить неполадки с профилем диспетчера трафика Azure, для которого отображается состояние "Деградация", и о том, как предоставить основные сведения о проверках диспетчера трафика.

После настройки профиля диспетчера трафика, указывающего на некоторые из размещенных служб .cloudapp.net, через несколько секунд отобразится состояние "Деградация".

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Если вы перейдете на вкладку "Конечные точки" этого профиля, то увидите одну или несколько конечных точек с состоянием "Не в сети":

![автономно](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Важные примечания о проверке диспетчера трафика

- Диспетчер трафика считает, что конечная точка находится "В СЕТИ", только если путь проверки передает ответ 200.
- Перенаправление 30x (или любой другой ответ, отличный от 200) приведет к сбою, даже если URL-адрес перенаправления вернет ответ 200.

- При проверках HTTP ошибки сертификата пропускаются.
 
- Фактическое содержимое пути проверки не имеет значения, если возвращается ответ 200. Обычно, если фактическое содержимое веб-сайта не возвращает ответ 200 (например, если страницы ASP выполняют перенаправление на страницу входа ACS или на другой URL-адрес CNAME), следует задать путь "/favicon.ico" или аналогичный.
 
- Рекомендуется указать путь проверки, содержащий достаточную логику, чтобы определить, работает сайт или нет. В примере параметра выше путь к "/favicon.ico" проверяется, только если w3wp.exe отвечает, но не в случае работоспособности веб-сайта. Оптимальнее будет задать путь "/Probe.aspx" и включить в Probe.aspx логику для определения работоспособности сайта (т. е. для проверки счетчиков производительности, что позволит убедиться в отсутствии загрузки ЦП 100 % или получении большого количества запросов со сбоем, для попытки доступа к ресурсам, например к состоянию базы данных или сеанса в целях проверки работоспособности логики приложения и т. д.).
 
- Если все конечные точки в профиле понижены, то диспетчер трафика будет считать их работоспособными и будет перенаправлять трафик во все конечные точки. Таким образом, любые возможные проблемы с механизмом проверки не приведут сбою службы.

  

## Устранение неполадок

Единое средство для устранения неполадок с проверкой диспетчера трафика — wget. Вы можете получить пакет двоичных файлов и зависимостей от [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Обратите внимание, что вы можете использовать другие программы, например Fiddler или curl, вместо wget — фактически подойдет любая программа, в которой можно отобразить необработанный ответ HTTP.

После установки wget перейдите в командную строку и запустите wget, указав URL-адрес, а также порт и путь проверки, настроенный в диспетчере трафика. Например, http://watestsdp2008r2.cloudapp.net:80/Probe.

![устранение неполадок](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Использование Wget:

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Обратите внимание: wget указывает, что URL-адрес вернул перенаправление 301 на http://watestsdp2008r2.cloudapp.net/Default.aspx. Как известно, в разделе "Важные примечания о проверке диспетчера трафика" выше диспетчер трафика считает перенаправление 30x сбоем, и это приводит к состоянию проверки "Не в сети". На этом этапе просто проверьте конфигурацию веб-сайта и убедитесь, что от пути /Probe возвращается ответ 200 (или перенастройте проверку диспетчера трафика так, чтобы она указывала на путь, который вернет ответ 200).

 

Если при проверке используется протокол HTTP, в средство wget потребуется добавить параметр "--no-check-certificate", чтобы в URL-адресе cloudapp.net игнорировалось несоответствие сертификатов.


## Дальнейшие действия


[О методах маршрутизации трафика в диспетчере трафика](traffic-manager-load-balancing-methods.md)

[Что такое диспетчер трафика](../traffic-manmager-overview.md)

[Облачные службы](http://go.microsoft.com/fwlink/?LinkId=314074)

[Веб-сайты](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Операции с диспетчером трафика (справочник по REST API)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Командлеты для диспетчера трафика Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_1210_2015-->