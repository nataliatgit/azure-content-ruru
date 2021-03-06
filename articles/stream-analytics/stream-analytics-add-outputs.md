<properties 
	pageTitle="Настройка выходов данных для заданий Stream Analytics | Microsoft Azure" 
	description="Настройка выходных данных для заданий Stream Analytics | Сегмент схемы обучения"
	keywords="вывод данных, перемещение данных"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>

# Настройка выходов данных для заданий Stream Analytics

Задания службы Azure Stream Analytics можно подключать к одному или нескольким выходам данных, которые определяют подключение к существующему приемнику данных. По мере того как задание Stream Analytics обрабатывает и преобразует входящие данные, поток выходных событий записывается в выходные данные задания.

Выходы данных Stream Analytics могут служить источником данных для панелей мониторинга в режиме реального времени и предупреждений, запускать рабочие процессы переноса данных или просто архивировать данные для последующей пакетной обработки. Служба Stream Analytics обеспечивает первоклассную интеграцию с различными службами Azure, которые подробно описаны здесь.

Чтобы добавить выход данных в задание службы Stream Analytics, выполните следующие действия.

1. На портале Azure нажмите **Выходные данные**, а затем **Добавить выходные данные** в задании Stream Analytics.

    ![Добавление выходных данных](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    На портале предварительной версии Azure нажмите плитку **Выходные данные** в задании Stream Analytics.

    ![Добавление выходных данных на портале Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Укажите тип выходных данных:

    ![Выбор типа перемещения данных](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Выбор типа перемещения данных на портале предварительной версии Azure](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. В поле **Псевдоним выходных данных** введите понятное имя для этого выхода данных. Впоследствии это имя можно будет использовать в запросе вашего задания для ссылки на выходные данные.
    
    Укажите остальные свойства подключения, необходимые для подключения к выходу данных. Эти поля зависят от типа выходных данных и подробно описаны здесь.

    ![Добавление свойств вывода данных](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. В зависимости от типа выходных данных необходимо указать способ сериализации или форматирования данных. В этой статье описаны конкретные параметры сериализации для каждого выходного типа.

    Укажите остальные свойства подключения, необходимые для подключения к источнику данных. Эти поля зависят от типа входных данных и источника и подробно описаны [здесь](stream-analytics-create-a-job.md).

    ![Добавление вывода данных в концентратор событий](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Вывод данных в концентратор событий на портале предварительной версии Azure](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

> [Azure.Note] Любой элемент вывода, добавляемый в задание, должен существовать до момента запуска задания и запуска потока событий. Например, при выводе данных в хранилище BLOB-объектов задание не создаст учетную запись хранения автоматически. Пользователь должен создать эту запись перед тем, как запустить задание ASA.

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->