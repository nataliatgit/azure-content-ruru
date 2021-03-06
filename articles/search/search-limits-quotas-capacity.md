<properties
	pageTitle="Ограничения Поиска Azure | Microsoft Azure | Размещенная облачная служба поиска"
	description="Ограничения службы поиска Azure, используемые при планировании мощности, а также ограничения запросов и ответов на них."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Ограничения поиска Azure

Ограничения хранилища, рабочих нагрузок, количества индексов, документов и других объектов зависят от того, добавили ли вы Поиск Azure в ценовую категорию **Бесплатный**, **Базовый** или **Стандартный**.

- Категория **Бесплатный** представляет собой мультитенантную службу, которая предоставляется вместе с подпиской Azure. Это вариант без дополнительных затрат для существующих подписчиков, который позволяет поэкспериментировать со службой до регистрации для использования выделенных ресурсов. 
- Категория **Базовый (предварительная версия)** предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде. Эта категория находится на этапе предварительной версии и предлагается по [сниженной цене](https://azure.microsoft.com/pricing/details/search/).
- Для категории **Стандартный** используются выделенные компьютеры, которые использует только ваша служба. Это обеспечивает увеличение емкости хранилища и вычислительной мощности на всех уровнях, даже при минимальной конфигурации. Поставляется два уровня "Стандартный" (S1 и S2). 

## Ограничения категории

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Число запросов в секунду (QPS) переменное, особенно в общей службе, так как пропускная способность зависит от доступной пропускной способности и конкуренции за системные ресурсы. Вычислительные ресурсы и ресурсы хранилища Azure, поддерживающие общую службу, используют несколько подписчиков. Таким образом, значение QPS для вашего решения всегда будет зависеть от количества выполняемых одновременно рабочих нагрузок. Для категории "Стандартный" можно точнее оценить QPS, так как вы можете контролировать больше параметров. См. раздел рекомендаций в статье [Управление решением поиска](search-manage.md), чтобы ознакомиться с указаниями по вычислению QPS рабочих нагрузок.

## Ограничения ключа API

Ключи API используются для проверки подлинности в службе. Существует два типа ключей. Ключи администратора указываются в заголовке запроса и предоставляют доступ к службе на чтение и запись. Ключи запросов доступны только для чтения, они указываются в URL-адресе и обычно передаются в клиентские приложения.

- Максимум 2 ключа администратора на одну службу.
- Максимум 50 ключей запросов на одну службу.

## Ограничения запросов

- Максимум 16 МБ на один запрос <sup>1</sup>
- Максимальная длина URL-адреса — 8 КБ.
- Максимум 1000 документов на одну операцию загрузки индексов, объединения или удаления.
- Максимум 32 поля в предложении $orderby.
- Максимальный размер поискового запроса — 32 766 байт (32 КБ минус 2 байта) текста в кодировке UTF-8.

## Ограничения ответа

- Максимум 1000 документов на одну страницу результатов поиска.
- Максимум 100 предложений на один запрос API предложений.

<sup>1</sup> В службе поиска Azure размер тела запроса должен быть не больше 16 МБ — это накладывает фактическое ограничение на отдельные поля или коллекции, для которых теоретические ограничения не установлены (дополнительные сведения о составе и ограничениях полей см. в разделе [Поддерживаемые типы данных](https://msdn.microsoft.com/library/azure/dn798938.aspx)).

<!-------HONumber=AcomDC_0302_2016-->