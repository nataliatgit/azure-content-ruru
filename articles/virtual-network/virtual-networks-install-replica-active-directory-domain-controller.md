<properties
	pageTitle="Установка реплики контроллера домена в Azure | Microsoft Azure"
	description="Учебник, в котором объясняется, как установить контроллер домена из леса локальной службы Active Directory на виртуальной машине Azure."
	services="virtual-network"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="virtual-network"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="curtand"/>


# Установка реплики контроллера домена Active Directory в виртуальной сети Azure

В этом разделе показано, как устанавливать дополнительные контроллеры домена (также известные как реплики контроллеров домена) для локального домена Active Directory на виртуальных машинах Azure (ВМ) в виртуальной сети Azure.

Вас также могут заинтересовать следующие связанные разделы:

-  При необходимости можно установить новый лес Active Directory в виртуальной сети Azure. Указания по выполнению этих действий см. в статье [Установка нового леса Active Directory в виртуальной сети Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Концептуальное руководство об установке доменных служб Active Directory (AD DS) в виртуальной сети Azure см. в статье [Руководства по развертыванию Windows Server Active Directory на виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## Схема сценария

В этом сценарии внешним пользователям требуется доступ к приложениям, работающим на серверах, входящих в домен. Виртуальные машины, которые запускаются на серверах приложений, и реплики контроллеров домена установлены в виртуальной сети Azure. Виртуальная сеть может подключаться к локальной сети по подключению [VPN типа "сеть-сеть"](../vpn-gateway/vpn-gateway-site-to-site-create.md), как показано на следующей схеме, или можно использовать [ExpressRoute](../../services/expressroute/) для более быстрого подключения.

Серверы приложений и контроллеры домена развернуты в отдельных облачных службах для распределения вычислительных ресурсов и в [группах доступности](../virtual-machines/virtual-machines-manage-availability.md) для повышения отказоустойчивости. Контроллеры домена реплицируются друг с другом и с локальными контроллерами домена с помощью репликации Active Directory. Средства синхронизации не требуются.

![][1]

## Создание сайта Active Directory для виртуальной сети Azure

Рекомендуется создать сайт в Active Directory, который представляет из себя сетевую область, соответствующую виртуальной сети. Это позволяет оптимизировать проверку подлинности, репликацию и другие операции, связанные с расположением контроллера домена. Ниже показано, как создать сайт. Дополнительную информацию см. в разделе [Добавление нового сайта](https://technet.microsoft.com/library/cc781496.aspx).

1. Откройте раздел «Службы и сайты Active Directory»: **Диспетчер серверов** > **Сервис** > **Службы и сайты Active Directory**.
2. Создайте сайт, представляющий область, в которой вы создали виртуальную сеть Azure: последовательно щелкните пункты **Сайты** > **Действие** > **Создать сайт**, введите имя нового сайта, например Azure US West, выберите ссылку на сайт и нажмите кнопку **ОК**.
3. Создайте подсеть и свяжите ее с новым сайтом: дважды щелкните **Сайты**, щелкните правой кнопкой мыши **Подсети** > **Создать подсеть**, введите диапазон IP-адресов виртуальной сети (например, 10.1.0.0/16 в схеме сценария), выберите новый сайт Azure и нажмите кнопку **ОК**.

## Создание виртуальной сети Azure

1. На классическом портале Azure выберите **Создать** > **Сетевые службы** > **Виртуальная сеть** > **Настраиваемое создание** и используйте следующие значения для завершения работы с мастером.

    На странице мастера… | Укажите такие значения
	------------- | -------------
	**Сведения о виртуальной сети** | <p>Имя: введите имя виртуальной сети, например WestUSVNet.</p><p>Регион: выберите ближайший регион.</p>
	**Подключение DNS и VPN** | <p>DNS-серверы: укажите имя и IP-адрес одного или нескольких локальных DNS-серверов.</p><p>Подключение: выберите **Настроить подключение VPN типа «сеть-сеть»**.</p><p>Локальная сеть: укажите новую локальную сеть.</p><p>При использовании ExpressRoute вместо VPN см. раздел [Настройка подключения ExpressRoute через поставщик Exchange](../expressroute/expressroute-configuring-exps.md).</p>
	**Подключение между сайтами** | <p>Имя: введите имя локальной сети.</p><p>IP-адрес устройства VPN: укажите общий IP-адрес устройства, которое будет подключено к виртуальной сети. VPN-устройство невозможно найти вне преобразования сетевых адресов (NAT). </p><p>Адрес: укажите диапазоны адресов локальной сети (например, 192.168.0.0/16 в схеме сценария).</p>
	**Адресные пространства виртуальной сети** | <p>Адресное пространство: укажите диапазон IP-адресов для виртуальных машин, которые требуется выполнить в виртуальной сети Azure (например, 10.1.0.0/16 в схеме сценария). Этот диапазон адресов не должен перекрываться диапазонами адресов локальной сети.</p><p>Подсети: укажите имя и адрес подсети для серверов приложений (например, Frontend, 10.1.1.0/24) и для контроллеров домена (например, внутренних, Backend, 10.1.2.0/24).</p><p>Щелкните **Добавить подсеть шлюза**.</p>

2. Далее вы настроите шлюз виртуальной сети, чтобы создать безопасное соединение VPN типа "сеть-сеть". Инструкции см. в разделе [Настройка шлюза виртуальной сети](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).
3. Создайте соединение VPN типа "сеть-сеть" между новой виртуальной сетью и локальным VPN-устройством. Инструкции см. в разделе [Настройка шлюза виртуальной сети](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).



## Создание виртуальных машин Azure для ролей контроллеров домена

Повторите перечисленные ниже шаги, чтобы создать виртуальные машины, чтобы при необходимости разместить роль контроллера домена. Следует развернуть по крайней мере два виртуальных контроллера домена, чтобы обеспечить отказоустойчивость и избыточность. Если виртуальная сеть Azure включает в себя по крайней мере два аналогично настроенных контроллера домена (то есть они оба являются глобальными, используются для размещения DNS-сервера, не содержат никакой роли FSMO и т. д.), поместите виртуальные машины, на которых выполняются эти контроллеры домена, в группу доступности для повышения отказоустойчивости. Чтобы создать виртуальные машины с помощью Windows PowerShell, а не пользовательского интерфейса, см. раздел [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md).

1. На классическом портале Azure щелкните **Создать** > **Вычислить** > **Виртуальная машина** > **Из коллекции**. Для завершения работы мастера используйте следующие значения. Настройте значение по умолчанию, если не рекомендуется и не требуется иное.

    На странице мастера… | Укажите такие значения
	------------- | -------------
	**Выбор образа** | Центр обработки данных Windows Server 2012 R2;
	**Конфигурация виртуальной машины** | <p>«Имя виртуальной машины» — введите одно описательное имя (например, AzureDC1).</p><p>«Новое имя пользователя» — введите имя пользователя. Этот пользователь будет входить в локальную группу администраторов на виртуальной машине. Это имя понадобится для входа на виртуальную машину в первый раз. Встроенная учетная запись «Администратор» не будет работать.</p><p>Новый пароль и подтверждение — введите пароль</p>.
	**Конфигурация виртуальной машины** | <p>«Облачная служба» — выберите <b>Создать новую облачную службу</b> для первой виртуальной машины и выберите это же имя облачной службы при создании дополнительных виртуальных машин, в которых будет размещена роль контроллера домена.</p><p>«DNS-имя облачной службы» — укажите глобальное уникальное имя</p><p>«Регион, территориальная группа, виртуальная сеть» — укажите имя виртуальной сети (например, WestUSVNet).</p><p>«Учетная запись хранения» — выберите <b>Использовать автоматически созданную учетную запись хранения</b> для первой виртуальной машины и выберите это же имя учетной записи хранения при создании дополнительных виртуальных машин, в которых будет размещена роль контроллера домена.</p><p>«Группа доступности» — выберите <b>Создать группу доступности</b>.</p><p> «Имя группы доступности» — введите имя группы доступности при создании первой виртуальной машины и выберите это же имя при создании других виртуальных машин.</p>
	**Конфигурация виртуальной машины** | <p>Выберите <b>Установить агент ВМ</b> и другие нужные расширения.</p>
2. Подключите диск к каждой виртуальной машине, на которой будет выполняться роль сервера контроллера домена. Дополнительный диск необходим для хранения базы данных AD, журналов и SYSVOL. Укажите размер диска (например, 10 ГБ) и в значении **Настройки кэша узла** выберите **Отсутствует**. Подробнее об этом см. в статье [Как присоединить диск данных к виртуальной машине](../virtual-machines/storage-windows-attach-disk.md).
3. После первого входа в виртуальную машину откройте **Диспетчер серверов** > **Файловые службы и службы хранилища** для создания тома на этом диске с использованием файловой системы NTFS.
4. Зарезервируйте статический IP-адрес для виртуальных машин, на которых будет выполняться роль контроллера домена. Чтобы зарезервировать статический IP-адрес, скачайте Установщик веб-платформ Майкрософт, [установите Azure PowerShell](../powershell-install-configure.md) и запустите командлет Set-AzureStaticVNetIP. Например:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Дополнительные сведения о настройке статического IP-адреса см. в разделе [Настройка статического внутреннего IP-адреса для виртуальной машины](../virtual-network/virtual-networks-reserved-private-ip.md).

## Установка служб AD DS на виртуальных машинах Azure

Войдите на виртуальную машину и убедитесь, что установили подключение через соединение VPN типа "сеть-сеть" или ExpressRoute к ресурсам в локальной сети. Затем установите службы AD DS на виртуальных машинах Azure. Можно выполнить действия, используемые для установки дополнительного контроллера домена в локальной сети (через пользовательский интерфейс, Windows PowerShell или файл ответов). При установке AD DS убедитесь, что указали новый том для расположения базы данных AD, журналов и SYSVOL. Чтобы вспомнить, как устанавливать службы AD DS, см. статью [Установка доменных служб Active Directory (уровень 100)](https://technet.microsoft.com/library/hh472162.aspx) или [Установка реплики контроллера домена Windows Server 2012 в существующем домене (уровень 200)](https://technet.microsoft.com/library/jj574134.aspx).

## Изменение конфигурации DNS-сервера для виртуальной сети

1. На классическом портале Azure щелкните имя виртуальной сети и вкладку **Настройка**, чтобы [изменить конфигурацию IP-адресов DNS-серверов для виртуальной сети](virtual-networks-manage-dns-in-vnet.md) для использования статических IP-адресов, назначенных репликам контроллеров домена вместо IP-адресов локальных DNS-серверов.

2. Чтобы убедиться, что все виртуальные машины реплики контроллера домена в виртуальной сети настроены на использование DNS-серверов в виртуальной сети, щелкните **Виртуальные машины**, щелкните столбец состояния для каждой виртуальной машины и выберите **Перезагрузить**. Подождите, пока для виртуальной машины отобразится состояние **Выполняется**, прежде чем пытаться войти в нее.

## Создание виртуальных машин для серверов приложений

1. Повторите перечисленные ниже шаги, чтобы создать виртуальные машины для использования в качестве серверов приложений. Настройте значение по умолчанию, если не рекомендуется и не требуется иное.


	На странице мастера… | Укажите такие значения
	------------- | -------------
	**Выбор образа** | Центр обработки данных Windows Server 2012 R2;
	**Конфигурация виртуальной машины** | <p>Имя виртуальной машины — введите одно описательное имя (например, AppServer1).</p><p>Новое имя пользователя — введите имя пользователя. Этот пользователь будет входить в локальную группу администраторов на виртуальной машине. Это имя понадобится для входа на виртуальную машину в первый раз. Встроенная учетная запись «Администратор» не будет работать.</p><p>Новый пароль и подтверждение — введите пароль</p>.
	**Конфигурация виртуальной машины** | <p>Облачная служба — выберите **Создать новую облачную службу** для первой виртуальной машины и выберите это же имя облачной службы при создании дополнительных виртуальных машин, в которых будет размещено приложение.</p><p>DNS-имя облачной службы — укажите глобальное уникальное имя</p><p>Регион, территориальная группа, виртуальная сеть — укажите имя виртуальной сети (например, WestUSVNet).</p><p>Учетная запись хранения — выберите **Использовать автоматически созданную учетную запись хранения** для первой виртуальной машины и выберите это же имя учетной записи хранения при создании дополнительных виртуальных машин, в которых будет размещено приложение.</p><p>Набор доступности — выберите **Создать группу доступности**.</p><p>Имя группы доступности — введите имя группы доступности при создании первой виртуальной машины и выберите это же имя при создании дополнительных виртуальных машин.</p>
	**Конфигурация виртуальной машины** | <p>Выберите <b>Установить агент ВМ</b> и другие нужные расширения.</p>

2. После подготовки войдите на каждую виртуальную машину и присоедините ее к домену. В окне **Диспетчер серверов** щелкните **Локальный сервер** > **РАБОЧАЯ ГРУППА** > **Изменить…**, а затем выберите **Домен** и введите имя локального домена. Предоставьте учетные данные пользователя домена, а затем перезапустите виртуальную машину, чтобы завершить присоединение к домену.

Чтобы создать виртуальные машины с помощью Windows PowerShell, а не пользовательского интерфейса, см. раздел [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md).

Дополнительные сведения об использовании Windows PowerShell см. в разделах [Приступая к работе с командтлетами Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) и [Справка по командлетам Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## Дополнительные ресурсы

-  [Рекомендации по развертыванию Windows Server Active Directory на виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Передача существующих локальных контроллеров домена Hyper-V в Azure с помощью Azure PowerShell](http://support.microsoft.com/kb/2904015)
-  [Установка нового леса Active Directory в виртуальной сети Azure](../active-directory-new-forest-virtual-machine.md)
-  [Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS для профессионалов в сфере IT. (01) Основная информация о виртуальных машинах](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS для профессионалов в сфере IT. (05) Создание виртуальных сетей и подключений между организациями](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Командлеты управления Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!---HONumber=AcomDC_0204_2016-->