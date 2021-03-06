<properties 
 pageTitle="Планы и выставление счетов в планировщике Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Планы и выставление счетов в планировщике Azure

## Тарифные планы коллекций заданий

Коллекции заданий в планировщике Azure тарифицируются. Коллекции содержат определенное число заданий и могут иметь один из трех описанных ниже планов: бесплатный, стандартный или премиальный.

|**Тарифный план коллекции заданий**|**Макс. число заданий в коллекции**|**Макс. повторяемость**|**Макс. число коллекции заданий на подписку**|**Ограничения**|
|:---|:---|:---|:---|:---|
|**Бесплатный**|5 заданий в коллекции|Один раз в час. Не позволяет выполнять задания чаще одного раза в час.|Подписка может включать до 1 бесплатной коллекции заданий.|Нельзя использовать [объект исходящей проверки подлинности HTTP](scheduler-outbound-authentication.md).
|**Стандартный**|50 заданий в коллекции|Один раз в минуту. Не позволяет выполнять задания чаще одного раза в минуту.|Подписка может включать до 100 бесплатных коллекций заданий.|Доступен весь набор функций планировщика.|
|**Премиальный**|50 заданий в коллекции|Один раз в минуту. Не позволяет выполнять задания чаще одного раза в минуту.|Подписка может включать до 10 000 премиальных коллекций заданий. <a href="mailto:wapteams@microsoft.com">Свяжитесь с нами</a> для получения дополнительных сведений.|Доступен весь набор функций планировщика.|

## Повышение и понижение тарифного плана коллекции заданий

Тарифный план коллекции заданий (бесплатный, стандартный или премиальный) можно повысить или понизить в любое время. При этом переход на бесплатную коллекцию заданий может закончиться неудачей по одной из следующих причин.

- В подписке уже существует бесплатная коллекция заданий.
- Повторяемость задания в коллекции превышает ограничение, установленное для заданий в бесплатной коллекции. Максимальная повторяемость заданий в бесплатной коллекции — один раз в час.
- Коллекция содержит больше 5 заданий.
- Задание в коллекции включает действие HTTP или HTTPS с использованием [объекта исходящей проверки подлинности HTTP](scheduler-outbound-authentication.md).

## Выставление счетов и тарифные планы Azure

При использовании бесплатных коллекций заданий подписка не оплачивается. Если у вас больше 100 стандартных коллекций заданий (10 стандартных единиц тарификации), рекомендуем перевести все коллекции заданий на премиальный план.

Если у вас есть одна стандартная и одна премиальная коллекция заданий, с вас будет взиматься одна стандартная _и_ одна премиальная единица тарификации. Служба планировщика оплачивается по количеству активных коллекций заданий, обозначенных как стандартные или как премиальные (см. следующие два раздела данной статьи).

## Стандартные единицы тарификации

Стандартная единица тарификации может включать до 10 стандартных коллекций заданий. Поскольку стандартная коллекция заданий может содержать до 50 заданий, одна стандартная единица тарификации позволяет включать в подписку до 500 заданий, что означает почти 22 миллиона выполненных заданий в месяц.

При наличии от 1 до 10 стандартных коллекций заданий оплачивается 1 стандартная единица тарификации. При наличии от 11 до 20 стандартных коллекций заданий оплачиваются 2 стандартные единицы тарификации. При наличии от 21 до 30 стандартных коллекций заданий оплачиваются 3 стандартные единицы тарификации.

## Премиальные единицы тарификации

Премиальная единица тарификации может включать до 10 000 премиальных коллекций заданий. Поскольку премиальная коллекция заданий может содержать до 50 заданий, одна премиальная единица тарификации позволяет включать в подписку до 500 000 заданий, что означает почти 22 миллиарда выполненных заданий в месяц.

При наличии от 1 до 10 000 премиальных коллекций заданий оплачивается 1 премиальная единица тарификации. При наличии от 10 001 до 20 000 премиальных коллекций заданий оплачиваются 2 премиальные единицы тарификации.

Таким образом, премиальные коллекции заданий работают точно так же, как и стандартные, но позволяют экономить, если необходимое приложению количество коллекций заданий очень велико.

## Выставление счетов и активный статус

Коллекции заданий всегда активны; исключение составляет только временное отключение всей подписки в связи с проблемами оплаты. Чтобы не оплачивать коллекцию заданий, либо переведите ее на _бесплатный_ тарифный план, либо удалите.

Отключение всех заданий коллекции можно выполнить одной операцией, но тарификация этой коллекции при этом не изменится — счет на такую коллекцию заданий будет выставляться _по-прежнему_. Точно так же считаются активными и подлежат оплате пустые коллекции заданий.

## Цены

Дополнительные сведения о ценах см. в разделе [Стоимость планировщика](https://azure.microsoft.com/pricing/details/scheduler/).

## См. также
 

 [Что такое планировщик?](scheduler-intro.md)
 
 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)

 [Исходящая аутентификация планировщика Azure](scheduler-outbound-authentication.md)
 
  

  

<!---HONumber=AcomDC_0128_2016-->