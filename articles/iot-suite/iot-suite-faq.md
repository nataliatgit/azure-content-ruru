<properties
  pageTitle="Часто задаваемые вопросы по Azure IoT Suite | Microsoft Azure"
  description="Часто задаваемые вопросы об IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="03/02/2016"
  ms.author="araguila"/>
   
# Часто задаваемые вопросы об IoT Suite

### Сколько экземпляров DocumentDB можно подготовить в рамках одной подписки?

Пять. Вы можете направить запрос в службу поддержки, чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только пять экземпляров DocumentDB. Иными словами, используя одну подписку, вы можете подготовить не более пяти предварительно настроенных решений для удаленного мониторинга.

### Сколько бесплатных API-интерфейсов Карт Bing можно подготовить в рамках одной подписки?

Два. В рамках одной подписки можно создать только два бесплатных API-интерфейса Карт Bing. Решение для удаленного мониторинга по умолчанию подготавливается с помощью бесплатного API-интерфейса Карт Bing. Иными словами, используя одну подписку, в которую не вносились изменения, вы можете подготовить не более двух решений для удаленного мониторинга.

### В чем разница между удалением группы ресурсов на портале Azure и нажатием кнопки "Удалить" в предварительно настроенном решении на сайте azureiotsuite.com?

- Если удалить предварительно настроенное решение на сайте [azureiotsuite.com][lnk-azureiotsuite], будут удалены все ресурсы, подготовленные при создании этого решения. Если вы добавляли в группу ресурсов дополнительные ресурсы, они также будут удалены. 

- Если удалить группу ресурсов на [портале Azure][lnk-azure-portal], будут удалены только ресурсы в этой группе. Вам также придется удалить приложение Azure Active Directory, связанное с предварительно настроенным решением, на [классическом портале Azure][lnk-classic-portal].

### Как удалить клиент AAD?

См. запись блога Эрика Голпа (Eric Golpe) [Пошаговое руководство по удалению клиента Azure AD][lnk-delete-aad-tennant].


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0309_2016-->