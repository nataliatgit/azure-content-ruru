<properties
   pageTitle="Использование кэша Redis для Azure с Java | Microsoft Azure"
	description="Приступая к работе с кэшем Redis для Azure с использованием Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Использование кэша Redis для Azure с Java

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Кэш Redis для Azure дает доступ к выделенному кэшу Redis, управляемому Майкрософт. Кэш доступен из любого приложения в Microsoft Azure.

В этом разделе показано, как приступить к работе с кэшем Redis для Azure, используя Java.


## Предварительные требования

[Jedis](https://github.com/xetorthio/jedis) — Java-клиент для Redis

В этом учебнике используется Jedis, но можно использовать любой клиент Java из перечисленных на сайте [http://redis.io/clients](http://redis.io/clients).


## Создание кэша Redis в Azure

На [портале Azure](http://go.microsoft.com/fwlink/?LinkId=398536) последовательно щелкните **Создать** и **Данные+хранилище**, а затем выберите **Кэш Redis**.

  ![][1]

Введите имя узла DNS. Оно будет иметь форму `<name>.redis.cache.windows.net`. Щелкните **Создать**.

  ![][2]


Создав кэш, щелкните его на портале Azure, чтобы просмотреть параметры кэша. Щелкните ссылку в разделе **Ключи** и скопируйте первичный ключ. Он потребуется для проверки подлинности запросов.

  ![][4]


## Включение конечной точки без SSL


Щелкните ссылку в разделе **Порты** и нажмите кнопку **Нет** для параметра «Разрешить доступ только через SSL». Это включит для кэша порт без SSL. Клиент Jedis в настоящее время не поддерживает SSL.

  ![][3]


## Добавление данных в кэш и их извлечение

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Дальнейшие действия

- [Включите диагностику кэша](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), чтобы можно было [наблюдать](https://msdn.microsoft.com/library/azure/dn763945.aspx) за работоспособностью кэша.
- Прочитайте официальную [документацию Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=AcomDC_0309_2016-->