<properties
	pageTitle="Рекомендованные дистрибутивы Linux | Microsoft Azure"
	description="Узнайте о рекомендованных дистрибутивах Linux в Azure, включая рекомендации по Ubuntu, OpenLogic, Oracle и SUSE."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"
	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="szark"/>



#Linux в Azure — рекомендованные дистрибутивы

Образы Linux в коллекции Azure или Marketplace предоставлены рядом партнеров, и мы также работаем с разными сообществами Linux, чтобы расширить список рекомендованных дистрибутивов. Пока же в случае отсутствия нужных дистрибутивов в коллекции вы всегда можете передать свой дистрибутив Linux, следуя указаниям [на этой странице](virtual-machines-linux-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Поддерживаемые дистрибутивы и версии ##

В следующей таблице перечислены дистрибутивы и версии Linux, поддерживаемые в Azure.

Драйверы Linux Integration Services (LIS) для Hyper-V и Azure представляют из себя модули ядра, которые Майкрософт встраивает непосредственно в основное ядро Linux. Драйверы LIS либо встроены в ядро дистрибутива по умолчанию, либо (для более старых дистрибутивов на основе RHEL или CentOS) доступны [здесь](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) в качестве отдельной загрузки. Дополнительные сведения о драйверах LIS см. в [этой ](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements) статье.

Агент Linux для Azure уже предварительно установлен в образах коллекции Azure и обычно доступен из репозитория пакета дистрибутива. Исходный код можно найти на сайте [GitHub](https://github.com/azure/walinuxagent).

Дистрибутив|Версия|Драйверы|Агент
---|---|---|---
CentOS через OpenLogic |CentOS 6.3+, 7.0+| CentOS 6.3: [скачиваемый пакет LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 и более поздних версий: в ядре|Пакет: в <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">репозитории OpenLogic в разделе WALinuxAgent<p><p>Источник: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)|494\.4.0 + |В ядре|Исходный код: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian от Credativ |Debian 7.9+, 8.2+|В ядре|Пакет: в репозитории в разделе WAAgent<p><p>Исходный код: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux| 6\.4+, 7.0+|В ядре|Пакет: в репозитории в разделе WALinuxAgent <p><p>Исходный код: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux. |RHEL 6.7 или более поздней версии, 7.1 или более поздней версии|В ядре|Пакет: в репозитории в разделе WALinuxAgent <p><p>Исходный код: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise |SLES 11 с пакетом обновления 3 или более поздней версии, SLES 12 или более поздней версии и <p><p>SLES для SAP 11.3 или более поздней версии |В ядре|Пакет: в репозитории [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) в разделе WALinuxAgent<p><p>Исходный код: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE |openSUSE 13.1+|В ядре|Пакет: в репозитории [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) в разделе WALinuxAgent <p><p>Исходный код: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 15.04 и 15.10|В ядре|Пакет: в репозитории в разделе WALinuxAgent <p><p>Исходный код: [GitHub](https://github.com/Azure/WALinuxAgent)
## Партнеры

### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic — ведущий поставщик корпоративных решений с открытым исходным кодом для облачных сред и центров обработки данных. OpenLogic помогает сотням ведущих предприятий в различных отраслях безопасно получать, поддерживать и контролировать программное обеспечение с открытым исходным кодом. Опираясь на экспертное сообщество, OpenLogic предлагает техническую поддержку коммерческого уровня и гарантии возмещения для 600 пакетов с открытым исходным кодом, в том числе поддержку корпоративного уровня для CentOS, а также является партнером по запуску для предоставления образов Centos в Azure.

### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

На веб-сайте CoreOS.

*Операционная система CoreOS обеспечивает безопасность, согласованность и надежность при работе. В CoreOS пакеты не устанавливаются через yum или apt. Для управления службами на более высоком уровне абстракции используются контейнеры Linux. Единый код службы и все зависимости упакованы в контейнер, который может выполняться на одном или нескольких компьютерах под управлением CoreOS.*


### Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ представляет собой независимую компанию, оказывающую консалтинговые и прочие услуги, которая специализируется на разработке и реализации профессиональных решений с помощью бесплатного программного обеспечения. Эксперты Credative получили международное признание многих использующих наши услуги ИТ-отделов как ведущие специалисты по открытому коду. Сейчас Credativ совместно с Майкрософт готовит соответствующие образы Debian для Debian 8 (Jessie) и версий, предшествующих Debian 7 (Wheezy). Они специально разработаны для работы в Azure, и ими можно легко управлять с помощью этой платформы. Credativ также поддерживают долгосрочное обслуживание и обновление образов Debian для Azure с помощью своих центров поддержки открытого кода.

### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle предлагает широкий набор решений для общедоступных и частных облаков, предоставляя при этом клиентам свободу выбора и гибкость при развертывании программного обеспечения Oracle в облаках Oracle, а также в других облаках. Партнерство Oracle с корпорацией Майкрософт позволяет клиентам развертывать программное обеспечение Oracle в общедоступных и частных облаках Майкрософт при обеспечении сертификации и поддержки от Oracle. Заинтересованность компании Oracle и ее инвестиции в решения для общедоступных и частных облаков Oracle остаются неизменными.

### Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Как ведущий мировой поставщик решений с открытым исходным кодом, Red Hat помогает более чем 90 % компаний, входящих в список Fortune 500, решать их бизнес-задачи, оптимизировать ИТ- и бизнес-стратегии и готовиться к дальнейшему развитию технологий. Для этого Red Hat предлагает защищенные решения, используя открытую бизнес-модель и доступную, прогнозируемую модель подписки.

### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server в Azure — проверенная платформа, предоставляющая высокую степень надежности и безопасности для работы в облаке. Универсальная платформа SUSE Linux легко интегрируется с облачными службами Azure, создавая облачную среду с простым управлением. Свыше 9200 сертифицированных приложений для SUSE Linux Enterprise Server от более чем 1800 независимых поставщиков программного обеспечения гарантируют, что рабочие нагрузки, поддерживаемые в центре обработки данных, могут быть беспрепятственно развернуты в Azure.

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Инженерно-техническое открытое сообщество Canonical способствует успешному распространению Ubuntu в клиентских, серверных и облачных средах, включая персональные облачные службы для потребителей. Canonical представляет Ubuntu как единую бесплатную платформу, от телефона до облака, с семейством согласованных интерфейсов для телефона, планшета, ТВ и рабочего стола, что делает Ubuntu лучшим вариантом для различных учреждений, от поставщиков общедоступных облаков до создателей бытовой электроники, и фаворитом среди частных специалистов.

Благодаря центрам разработки и проектирования по всему миру Canonical имеет уникальные возможности для партнерства с производителями оборудования, поставщиками контента и разработчиками программного обеспечения для вывода на рынок решений Ubuntu для разных сред, от компьютеров до серверов и портативных устройств.

<!---HONumber=AcomDC_1210_2015-->