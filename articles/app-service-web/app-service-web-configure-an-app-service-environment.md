<properties 
	pageTitle="Настройка среды службы приложений" 
	description="Настройка и мониторинг среды службы приложений, а также управление ею" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Настройка среды службы приложений #

## Обзор ##

Среды службы приложений (ASE) — это решение службы приложений Azure уровня Премиум, которое предоставляет новые возможности масштабирования и доступа к сети. Эта новая возможность масштабирования позволяет разместить экземпляр службы приложений Azure в виртуальной сети. Кроме этого, в среде ASE можно размещать веб-приложения, мобильные приложения и приложения API. Приложения логики пока не поддерживаются.

Если вы не знакомы со средой службы приложений, см. статью [Знакомство со средой службы приложений](app-service-app-service-environment-intro.md). Сведения о том, как создать такую среду, см. в статье [Создание среды службы приложений](app-service-web-how-to-create-an-app-service-environment.md).

На высоком уровне среда службы приложений состоит из нескольких основных компонентов:

- вычислительные ресурсы, работающие в размещенной службе среды приложений Azure;
- Хранилище
- База данных
- Классическая виртуальная сеть v1, включающая по крайней мере одну подсеть;
- подсеть, в которой работает размещенная служба среды приложений Azure.

Вычислительные ресурсы используются в четырех пулах ресурсов. Каждая среда ASE включает набор внешних интерфейсов и три рабочих пула. Использовать все 3 рабочих пула необязательно. При необходимости можно использовать только один. Внешние интерфейсы являются конечными точками HTTP для приложений в среде ASE. Фактически, приложения выполняются в рабочих ролях. Необходимость добавления дополнительных внешних интерфейсов или рабочих ролей зависит от особенностей работы приложений, размещенных в среде ASE. В качестве примера предположим, что в среде ASE есть всего одно приложение — Hello World, которое получает огромное количество запросов. В таком случае вам нужно увеличить количество внешних интерфейсов, чтобы обеспечить обработку нагрузки HTTP, но без увеличения количества рабочих ролей. Сделать это вручную довольно сложно, особенно если учесть, что в каждой среде ASE, скорее всего, выполняются разные приложения с разными критериями производительности. Чтобы упростить выполнение задач подобного рода, мы включили в среды ASE возможность автоматического масштабирования. Дополнительную информацию о масштабировании и автоматическом масштабировании в средах ASE см. в статье [Настройка автоматического масштабирования в среде службы приложений][ASEAutoscale].

Каждая среда ASE предоставляется с хранилищем емкостью 500 ГБ. Это пространство используют все размещенные в среде ASE приложения. Хранилище является частью среды ASE, и в настоящее время вы не можете использовать вместо него пространство пользовательского хранилища.

Информация о среде и выполняющихся в ней приложениях хранится в базе данных. Хотя эта база данных также предоставляется в рамках подписки Azure, пользователи не могут выполнять с ней какие-либо действия напрямую.

С ASE можно использовать существующую виртуальную сеть или ту, которая была создана одновременно со средой. Чтобы среда ASE и VNET были включены в разные группы ресурсов, вам нужно создавать эти ресурсы отдельно. А вот подсеть, которая будет использоваться, рекомендуется создавать одновременно со средой ASE, чтобы ASE была включена в ту же группу ресурсов, что и виртуальная сеть. В настоящее время поддерживаются только классические виртуальные сети V1.

Пользовательский интерфейс для мониторинга среды ASE и управления ею доступен на портале Azure. Если у вас есть среда ASE, возможно, вы захотите, чтобы на боковой панели отображался значок службы приложений. Этот значок используется для обозначения сред ASE на портале Azure.

![][1]

Щелкните этот значок или шеврон (знак «больше чем») в нижней части боковой панели, а затем выберите «Среды службы приложений». Оба эти действия открывают пользовательский интерфейс со списком всех сред ASE. Выберите одну из приведенных сред ASE, чтобы открыть пользовательский интерфейс для мониторинга и управления.

![][2]

В первой колонке отображаются некоторые свойства среды ASE, а также диаграмма с метриками для пула ресурсов. Некоторые свойства представлены в разделе «Основные компоненты» в виде гиперссылок. Если щелкнуть какую-либо из них, откроется связанная колонка. Например, можно щелкнуть имя виртуальной сети, чтобы открыть пользовательский интерфейс виртуальной сети, в которой выполняется среда ASE. Если щелкнуть «Планы службы приложений» и «Приложения», откроются колонки со списком соответствующих элементов, размещенных в среде ASE.

## Мониторинг ##

На диаграммах отображены разные метрики производительности для каждого пула ресурсов. Что касается пула внешних интерфейсов, целесообразно отслеживать средние показатели использования ЦП и памяти. Внешние интерфейсы отличаются распределенной нагрузкой, поэтому на основе средних показателей можно получить представление об общей производительности. С рабочими пулами все обстоит иначе. Рабочие роли, входящие в рабочий пул, могут использоваться несколькими планами службы приложений. В таком случае показатели использования ЦП и памяти не будут особенно информативными. Важнее отслеживать количество используемых и доступных рабочих ролей, особенно если вы управляете этой системой для других пользователей.

Все метрики, которые можно отслеживать на диаграммах, можно также использовать для настройки оповещений. Настройка оповещений является стандартной процедурой в рамках службы приложений. Настроить оповещение можно с помощью пользовательского интерфейса оповещений. Также вы можете выбрать команду «Добавить оповещение» в пользовательском интерфейсе нужной метрики.
 
![][3]

Упомянутые выше метрики — это метрики среды ASE. Но метрики также доступны на уровне плана службы приложений (ASP). Именно на этом уровне целесообразно отслеживать показатели использования ЦП и памяти. Все планы ASP в среде ASE являются выделенными. Иными словами, в план ASP включены только те приложения, которые запущены на выделенных этому плану узлах. Чтобы просмотреть подробную информацию о плане ASP, щелкните нужный план в любом списке в пользовательском интерфейсе среды ASE или выберите его из полного списка планов в разделе «Планы службы приложений».

Возможно, вы уже знакомы с функцией автоматического масштабирования планов ASP, не связанных со средой ASE, на основе метрик, доступных для ресурса. В среде ASE эта функция работает аналогичным образом. В среде в ASE доступно не только автоматическое масштабирование планов ASP на основе метрик. Вы также можете настроить правила автоматического масштабирования для самой среды ASE. Подробное руководство по настройке автоматического масштабирования см. в статье [Настройка автоматического масштабирования в среде службы приложений][ASEAutoscale].


## Свойства ##

Когда вы переходите в колонку ASE, автоматически открывается колонка «Параметры». В верхней части колонки находится раздел «Свойства». Наряду с элементами, которые повторяют данные из раздела «Основные компоненты», здесь также отображены и другие полезные сведения, включая виртуальный IP-адрес и исходящий IP-адрес. Сейчас адреса одинаковы, но со временем вам может понадобиться использовать исходящий IP-адрес, который будет отличаться от виртуального IP-адреса. Поэтому они оба отображены здесь. Таким образом, если вы не будете настраивать SSL и добавлять отдельный IP-адрес для одного приложения в среде ASE, IP-адрес, заданный в DNS для созданных в ASE приложений, будет совпадать с виртуальным IP-адресом, отображенным в разделе «Свойства».

![][4]

## IP-адреса ##

В этой колонке можно добавлять дополнительные IP-адреса, которые будут использоваться для приложений ASE. При создании в среде ASE приложения, для которого требуется настроить SSL на основе IP, это приложение должно получить отдельный IP-адрес. Для этого в среде ASE должны быть IP-адреса, которые можно выделить. В созданной среде ASE для этой цели доступен только один адрес. Если требуются дополнительные адреса, добавьте их в этой колонке. Прежде чем добавить максимальное количество выделяемых адресов, следует учитывать, что за дополнительные IP-адреса взимается плата. Подробная информация о максимальном количестве приведена на странице [Цены на службу приложений][AppServicePricing]. Просто прокрутите статью вниз до раздела о SSL-подключениях. Дополнительные цена — цена за SSL на основе IP.

**ПРИМЕЧАНИЕ.** Добавление дополнительных IP-адресов — это операция масштабирования. Одновременно может выполняться только одна такая операция. Существует три типа операций масштабирования:

- изменение количества IP-адресов, доступных в ASE для использования SSL на основе IP;
- изменение объема вычислительных ресурсов, используемых в пуле ресурсов;
- изменение объема вычислительных ресурсов, используемых в пуле ресурсов, вручную либо с помощью автоматического масштабирования.

## Пулы ресурсов ##

Из колонки «Параметры» можно перейти в пользовательский интерфейс пула внешних интерфейсов или рабочего пула. В каждой из колонок пулов ресурсов можно просмотреть информацию об определенном пуле ресурсов и воспользоваться элементами управления, позволяющими полностью масштабировать этот пул ресурсов.

В основной колонке каждого пула ресурсов приведена диаграмма с метриками для этого пула. Как и с диаграммами в колонке ASE, вы можете перейти к параметрам диаграммы и настроить оповещения. Настройка оповещений для определенного пула ресурсов в колонке ASE аналогична настройке оповещений непосредственно в параметрах пула ресурсов. В колонке «Параметры» рабочего пула можно выбрать отображение списка всех приложений или планов ASP, запущенных в этом рабочем пуле.

![][5]

### Масштабирование количества вычислительных ресурсов ###

Если вы хотите узнать больше о масштабировании приложений в среде ASE, см. подробное руководство в статье [Масштабирование приложений в среде службы приложений](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Дополнительную информацию о настройке автоматического масштабирования для пулов ресурсов в среде ASE см. в статье [Автоматическое масштабирование в среде службы приложений][ASEAutoscale]. Далее подробно описывается ручное масштабирование пулов ресурсов.

Пулы внешних интерфейсов и рабочих ролей недоступны клиентам напрямую. Иными словами, нельзя получить доступ к ним через RDP, вмешаться в процесс их подготовки или управлять ими в качестве администратора. Эти ресурсы выполняются и поддерживаются средой Azure. Но при этом количество и объем вычислительных ресурсов задает пользователь.

Существует три способа управления количеством серверов в пулах ресурсов: 1) выполнение масштабирования в основной колонке ASE, расположенной сверху; 2) выполнение ручного масштабирования определенного пула ресурсов в колонке «Масштабирование», которую можно открыть через раздел «Параметры»; 3) выполнение автоматического масштабирования в колонке «Масштабирование» определенного пула ресурсов.

Чтобы выполнить масштабирование в колонке ASE, просто щелкните ее, перетащите ползунок до требуемого количества и сохраните изменения. Здесь можно также изменить объем.

![][6]

Чтобы воспользоваться возможностями ручного или автоматического масштабирования определенного пула ресурсов, откройте колонку ASE и перейдите в раздел «Параметры». В колонке «Параметры» откройте пул внешних интерфейсов или рабочий пул, а затем перейдите к пулу, который нужно изменить. В колонке «Параметры» возле пункта «Масштабирование» есть знак шеврона. Если щелкнуть этот знак, откроется колонка, в которой можно выполнить ручное или автоматическое масштабирование.

![][7]

Среду службы приложений можно настроить для использования до 55 вычислительных ресурсов. Из этих 55 вычислительных ресурсов только 50 можно использовать для размещения рабочих нагрузок. Для этого есть две причины. Существует как минимум 2 вычислительных ресурса для интерфейсов. Остается максимум 53 ресурса для поддержки распределения пула работников. Для обеспечения отказоустойчивости необходимо иметь дополнительные вычислительные ресурсы, выделенные в соответствии со следующими правилами:

- для каждого рабочего пула требуется по крайней мере один дополнительный вычислительный ресурс, которому нельзя назначать рабочую нагрузку;
- если количество вычислительных ресурсов в рабочем пуле превышает определенное значение, для обеспечения отказоустойчивости требуется другой вычислительный ресурс. С пулом внешних интерфейсов дела обстоят иначе.

Согласно требованиям к отказоустойчивости любого пула работников данное значение X ресурсов назначается пулу работников при таких условиях:

- если переменная X — это число от 2 до 20, количество доступных вычислительных ресурсов, которые можно использовать для рабочих нагрузок, равно X-1;
- если переменная X — это число от 21 до 40, количество доступных вычислительных ресурсов, которые можно использовать для рабочих нагрузок, равно X-2;
- если переменная X — это число от 41 до 53, количество доступных вычислительных ресурсов, которые можно использовать для рабочих нагрузок, равно X-3.

Следует помнить, что минимальная конфигурация включает два внешних сервера и две рабочих роли. Вот несколько примеров, объясняющих описанное выше.

- Если в одном пуле есть 30 рабочих ролей, для размещения рабочих нагрузок можно использовать 28. 
- Если в одном пуле есть две рабочих роли, для размещения рабочих нагрузок можно использовать одну.
- Если в одном пуле есть 20 рабочих ролей, для размещения рабочих нагрузок можно использовать 19.  
- Если в одном пуле есть 21 рабочая роль, для размещения рабочих нагрузок можно использовать только 19.  

Обеспечение отказоустойчивости — это важный аспект, однако при выполнении масштабирования сверх определенных пороговых значений следует помнить об упомянутых условиях. Если требуется увеличить емкость, имея 20 экземпляров, нужно использовать 22 экземпляра и более, так как вы не добьетесь увеличения емкости, используя 21 экземпляр. Аналогично, чтобы увеличить емкость, имея 40 экземпляров, нужно 42 экземпляра.

### Масштабирование объема вычислительных ресурсов ###

Вы можете не только управлять количеством вычислительных ресурсов, которые можно назначить данному пулу, но и контролировать размер вычислительного ресурса. Для сред службы приложений можно выбрать четыре разных размера с метками от P1 до P4. Сведения о размерах и их стоимости см. в статье [Цены службы приложений](../app-service/app-service-value-prop-what-is.md). Размеры с P1 по P3 — это те же размеры, что обычно доступны. Вычислительный ресурс размера P4 состоит из 8 ядер с 14 ГБ ОЗУ и доступен только в среде службы приложений.

Объем вычислительных ресурсов, используемых в пулах, можно изменить двумя способами. Можно выполнить команду масштабирования в колонке ASE или воспользоваться колонкой «Ценовые категории», которую можно открыть в колонке «Параметры» определенного пула ресурсов.

![][8]

Однако перед внесением изменений нужно учесть некоторые моменты.

- Процесс внесения изменений может длиться в течение нескольких часов (зависит от объема изменений).
- Когда вы изменяете конфигурацию среды службы приложений, нельзя запустить второе изменение.
- Изменение объема вычислительных ресурсов, используемых в рабочем пуле, может привести к простою приложений, запущенных в этом пуле.

Добавление экземпляров в рабочий пул — это простая операция, которая не приводит к простою приложений, связанных с ресурсами в этом пуле. А вот изменение объема вычислительных ресурсов — это совсем другая история. Чтобы избежать простоя приложений при изменении объема рабочего пула, сделайте следующее:

- вызовите экземпляры, нужные для достижения требуемого размера, с помощью неиспользуемого пула работников;
- измените масштаб планов службы приложений для работы с новым пулом работников.  
 
Работе запущенных приложений это помешает значительно меньше, чем изменение объема вычислительных ресурсов при активных рабочих нагрузках. Дополнительную информацию о масштабировании приложений в среде ASE см. в статье [Масштабирование приложений в среде службы приложений](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

## Виртуальная сеть ##

В отличие от размещенной службы, которая содержит среду ASE, [виртуальной сетью][virtualnetwork] и подсетью управляет пользователь. Среда ASE имеет некоторые требования к сети, но все остальные параметры определяются пользователем. Вот какие требования имеет среда службы приложений:

- классическая виртуальная сеть v1; 
- подсеть, включающая по крайней мере 8 адресов; 
- виртуальная сеть должна быть региональной.  
 
Администрирование виртуальной сети выполняется через пользовательский интерфейс виртуальной сети или Powershell.

Так как эта возможность помещает службу приложений Azure в виртуальную сеть, это значит, что ваши приложения, размещенные в ASE, могут теперь получать доступ к ресурсам, теперь доступны ресурсы через ExpressRoute или напрямую через VPN типа "сеть-сеть". Чтобы использовать ресурсы, доступные в сети, в которой размещена среда службы приложений, приложениям этой среды не нужны дополнительные сетевые компоненты. Это означает, что для размещения ресурсов в виртуальной сети или их подключения к этой сети вам не нужно выполнять интеграцию VNET или использовать гибридные подключения. Тем не менее эти функции можно использовать для доступа к ресурсам в сетях, которые не подключены к используемой виртуальной сети. Например, интеграцию VNET можно использовать для интеграции с виртуальной сетью, которая доступна в рамках подписки, но не подключена к виртуальной сети, в которой размещена среда ASE. Гибридные подключения можно использовать для получения доступа к ресурсам в других сетях в стандартном режиме.

Если для виртуальной сети настроено использование ExpressRoute или VPN, следует учитывать некоторые требования к маршрутизации в среде ASE. Некоторые конфигурации определяемых пользователем маршрутов не поддерживаются в среде ASE. Дополнительную информацию о настройке виртуальной сети с ExpressRoute для среды ASE см. в статье [Настройка виртуальной сети с ExpressRoute для среды службы приложений][ExpressRoute].

Можно также управлять доступом к приложениям с помощью групп безопасности сети. Эта возможность позволяет настроить среду службы приложений так, чтобы в ней использовались только нужные вам IP-адреса. Дополнительные сведения о том, как это сделать, см. в статье [Управление входящим трафиком в среде службы приложений](app-service-app-service-environment-control-inbound-traffic.md).

## Удаление среды службы приложений ##

Если вы хотите удалить среду службы приложений, просто воспользуйтесь действием удаления в верхней части колонки среды. После этого появится запрос на ввод имени среды ASE, чтобы вы могли подтвердить удаление. ПРИМЕЧАНИЕ. При удалении среды ASE также удаляется все ее содержимое.

![][9]

## Приступая к работе

Чтобы начать работу со средами службы приложений, см. статью [Создание среды службы приложений](app-service-web-how-to-create-an-app-service-environment.md).

Дополнительные сведения о платформе службы приложений Azure см. в статье [Служба приложений Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0128_2016-->