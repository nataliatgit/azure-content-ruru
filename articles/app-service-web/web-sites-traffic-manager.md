<properties 
	pageTitle="Управление трафиком веб-приложений Azure с помощью диспетчера трафика Azure" 
	description="В этой статье представлена сводная информация об использовании диспетчера трафика Azure с веб-приложениями Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="cephalin"/>

# Управление трафиком веб-приложений Azure с помощью диспетчера трафика Azure

> [AZURE.NOTE] В этой статье представлена сводная информация об использовании диспетчера трафика Microsoft Azure с веб-приложениями службы приложений Azure. Дополнительные сведения о самом компоненте диспетчера трафика Azure можно найти по ссылкам, приведенным в конце статьи.

## Введение
С помощью диспетчера трафика Azure можно управлять распределением запросов от веб-клиентов между веб-приложениями в службе приложений Azure. После добавления конечных точек веб-приложений Azure в профиль диспетчера трафика Azure этот компонент отслеживает состояние ваших веб-приложений (запущены, остановлены или удалены). Таким образом диспетчер трафика может принимать решения о том, на какие из этих конечных точек нужно перенаправлять трафик.

## Методы балансировки нагрузки
Диспетчер трафика Azure использует три разных метода балансировки нагрузки. Они описаны в приведенном ниже списке в контексте их использования с веб-приложениями Azure.

* **Отработка отказа**. Если у вас есть клоны веб-приложения в разных регионах, с помощью этого метода одно веб-приложение можно настроить на обслуживание всего трафика веб-клиентов, а другое веб-приложение в другом регионе — на обслуживание этого же трафика в случае недоступности первого веб-приложения. 
	
* **Циклический перебор**. Если у вас есть клоны веб-приложения в разных регионах, с помощью этого метода можно равномерно распределять трафик между веб-приложениями в разных регионах.
	
* **Производительность**. Метод производительности распределяет трафик на основе минимального времени обмена данными для клиентов. Этот метод можно использовать для веб-приложений, расположенных как в одном, так и в разных регионах.

Дополнительные сведения о балансировке нагрузки в диспетчере трафика Azure см. в разделе [Описание методов балансировки нагрузки в диспетчере трафика](../traffic-manager/traffic-manager-load-balancing-methods.md).

##Веб-приложения и профили диспетчера трафика 
Чтобы настроить управление трафиком веб-приложений, создайте в диспетчере трафика Azure профиль, использующий один из трех описанных выше методов распределения нагрузки. Затем добавьте в этот профиль конечные точки (в данном случае это веб-приложения), трафиком которых требуется управлять. Сведения о состоянии веб-приложений (запущены, остановлены или удалены) регулярно передаются в профиль, что дает возможность диспетчеру трафика Azure соответствующим образом перенаправлять трафик.

При использовании диспетчера трафика Azure с Azure необходимо помнить следующее:

* В случае развертывания в одном регионе только веб-приложений служба приложений уже предоставляет возможности отработки отказа и циклического перебора вне зависимости от режима работы веб-приложения.

* В случае развертывания в одном регионе веб-приложений и других облачных служб Azure вы можете сочетать оба типа конечных точек для реализации гибридных сценариев.

* В профиле можно указать только одну конечную точку веб-приложения на регион. При выборе веб-приложения в качестве конечной точки для одного региона остальные веб-приложения в этом регионе становятся недоступны для выбора.

* Конечные точки веб-приложений, указанные в профиле диспетчера трафика Azure, появляются в профиле на странице настройки веб-приложения в разделе **Доменные имена**, но настроить их там невозможно.

* Если добавить веб-приложение в профиль, на странице портала веб-приложения на панели мониторинга в поле **URL-адрес** сайта будет отображаться URL-адрес личного домена этого веб-приложения (если он был задан). В противном случае он будет отображать URL-адрес профиля диспетчера трафика (например, `contoso.trafficmgr.com`). На странице настройки веб-приложения в разделе **Доменные имена** будут отображаться прямое доменное имя веб-приложения и URL-адрес диспетчера трафика.

* Имена личных доменов будут работать должным образом, но, помимо их добавления в веб-приложения, необходимо также настроить сопоставление DNS таким образом, чтобы оно указывало на URL-адрес диспетчера трафика. Информацию о настройке пользовательского домена для веб-приложения Azure см. в разделе [Настройка пользовательского имени домена для веб-сайта Azure](web-sites-custom-domain-name.md).

* В профиль диспетчера трафика Azure можно добавлять только веб-приложения, работающие в стандартном режиме.

## Дальнейшие действия

Общие и технические сведения о диспетчере трафика Azure см. в разделе [Обзор диспетчера трафика](../traffic-manager/traffic-manager-overview.md).

Дополнительные сведения о балансировке нагрузки в диспетчере трафика Azure см. в разделе [Описание методов балансировки нагрузки в диспетчере трафика](../traffic-manager/traffic-manager-load-balancing-methods.md).

Дополнительные сведения об использовании диспетчера трафика совместно с веб-приложениями см. в статьях [Использование диспетчера трафика Azure c веб-сайтами Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) и [Диспетчер трафика Azure теперь можно интегрировать с веб-сайтами Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
 

<!---HONumber=AcomDC_0302_2016-->