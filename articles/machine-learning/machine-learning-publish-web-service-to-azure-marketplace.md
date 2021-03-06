<properties 
	pageTitle="Публикация веб-службы машинного обучения в Azure Marketplace | Microsoft Azure" 
	description="Публикация веб-службы машинного обучения Azure в Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2016" 
	ms.author="bharaths"/>

# Публикация веб-службы Машинного обучения Azure в Azure Marketplace 

В магазине Azure Marketplace можно публиковать веб-службы машинного обучения Azure как платные или бесплатные службы для внешних клиентов. В этой статье приводится обзор процесса публикации со ссылками на руководства для начала работы. С помощью этой процедуры можно разрешить другим разработчикам использовать веб-службы в их приложениях.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Обзор процесса публикации 

Чтобы опубликовать веб-службу Машинного обучения Azure в Azure Marketplace, сделайте следующее:

1. Создайте и опубликуйте службу машинного обучения типа «запрос-ответ» (RRS).
2. Разверните службу в рабочей среде и получите информацию о ключе API и конечной точке OData.
3. Используйте URL-адрес опубликованной веб-службы для публикации в [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/). 
4. После отправки это предложение будет рассмотрено и должно быть утверждено, прежде чем клиенты смогут начать приобретать его. Процесс публикации может занять несколько рабочих дней. 

## Пошаговое руководство
###Шаг 1. Создание и публикация службы машинного обучения типа «запрос-ответ» (RRS)###
 Если вы еще не создали такую службу, ознакомьтесь с этим [пошаговым руководством](machine-learning-walkthrough-5-publish-web-service.md).

###Шаг 2. Развертывание службы в рабочей среде, а также получение информации о ключе API и конечной точке OData.###
1. На [классическом портале Azure](http://manage.windowsazure.com) выберите элемент **МАШИННОЕ ОБУЧЕНИЕ** на панели навигации слева, а затем рабочую область. 

2. Щелкните вкладку **ВЕБ-СЛУЖБЫ** и выберите веб-службу, которую нужно опубликовать в Marketplace.

	![Azure Marketplace][workspace]

3. Выберите конечную точку, которую необходимо использовать в Marketplace. Если вы не создали дополнительные конечные точки, можно выбрать конечную точку **По умолчанию**.

4. Щелкнув конечную точку, вы сможете увидеть **КЛЮЧ API**. Скопируйте его. Эта информация потребуется далее на шаге 3.

	![Azure Marketplace][apikey]

5. Щелкните метод **ЗАПРОС-ОТВЕТ**. На этом этапе публикация служб пакетного выполнения в Marketplace не поддерживается. Вы перейдете на страницу справки API для метода «Запрос-ответ».

6. Скопируйте **адрес конечной точки OData**. Эта информация потребуется далее на шаге 3.

	![Azure Marketplace][odata]




Разверните службу в рабочей среде.



###Шаг 3. Использование URL-адреса опубликованной веб-службы для публикации в Azure Marketplace (DataMarket)###

1.  Перейдите к [Azure Marketplace (Data Market)](http://datamarket.azure.com/home). 
2.  Щелкните ссылку **Опубликовать** в верхней части страницы. Откроется страница [портала публикации Microsoft Azure](https://publish.windowsazure.com).
3.  Щелкните раздел **Издатели**, чтобы зарегистрироваться как издатель.
4.	При создании нового предложения выберите пункт **Службы данных**, а затем выберите команду **Создать новую службу данных**. 
 
	![Azure Marketplace][image1]

	<br />


5.	На вкладке **Планы** укажите информацию о предложении, включая тарифный план. Определите, будет ли предложение платной или бесплатной службой. Для получения оплаты необходимо указать платежные реквизиты, такие как банковские и налоговые данные.

6.	На вкладке **Маркетинг** укажите информацию о предложении, такую как заголовок и описание предложения.

7.	На вкладке **Цены** можно установить цену для планов в конкретных странах или позволить системе автоматически назначить цену для вашего предложения.

8. На вкладке **Служба данных** выберите для параметра **Источник данных** значение **Веб-служба**.

	![Azure Marketplace][image2]

9.	Откройте информацию об URL-адресе и ключе API веб-службы, полученных на классическом портале Azure, как описано на шаге 2 выше.

10.	В диалоговом окне настройки службы данных Marketplace в текстовом поле **URL-адрес службы** вставьте адрес конечной точки OData.

11. Для **аутентификации** выберите для параметра **Схема аутентификации** значение **Заголовок**.

	- В поле **Имя заголовка** укажите Authorization.
	- В поле **Значение заголовка** введите Bearer (без кавычек), щелкните строку **пространства**, а затем вставьте ключ API.
	- Установите флажок **Это служба OData**.
	- Нажмите кнопку **Проверить подключение**, чтобы проверить подключение.

12.	На вкладке **Категории** выберите **Машинное обучение**.

13. После ввода всех метаданных о вашем предложении нажмите кнопку **Опубликовать**, а затем щелкните **Переместить в промежуточную среду**. На этом этапе вы получите уведомление об оставшихся нерешенных проблемах.

14. Если все проблемы решены, щелкните **Запросить утверждение для перемещения в рабочую среду**. Процесс публикации может занять несколько рабочих дней.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=AcomDC_0218_2016-->