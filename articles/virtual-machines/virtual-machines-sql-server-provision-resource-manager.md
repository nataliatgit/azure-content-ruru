<properties
	pageTitle="Подготовка виртуальной машины SQL Server в диспетчере ресурсов Azure | Microsoft Azure"
	description="Создание виртуальной машины SQL Server в режиме диспетчера ресурсов Azure. В этом учебнике в основном используется пользовательский интерфейс и инструменты, а не сценарии."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Подготовка виртуальной машины SQL Server в диспетчере ресурсов Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

В этом комплексном руководстве показано, как подготовить виртуальную машину Azure на портале с помощью модели диспетчера ресурсов Azure модели и настроить SQL Server на основе шаблона из коллекции Azure.

Коллекция виртуальных машин (VM) в Azure включает в себя несколько образов, содержащих Microsoft SQL Server. Вы можете выбрать один из образов VM из коллекции и в несколько действий подготовить VM в среде Azure.

Изучив данный учебник, вы научитесь:

- [Подключаться к порталу Azure и подготавливать образ виртуальной машины SQL из коллекции с помощью модели развертывания диспетчера ресурсов](#Provision).

- [Настраивать виртуальную машину и параметры SQL Server](#ConfigureVM).

- [Подключаться к виртуальной машине через удаленный рабочий стол](#Open).

- [Подключаться к экземпляру SQL Server с помощью SQL Server Management Studio на другом компьютере](#Connect).

- [Дальнейшие действия](#Next)

Данное руководство предполагает наличие учетной записи Azure. Если у вас нет учетной записи Azure, используйте [бесплатную пробную версию Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a id="Provision">Подготовка образа VM SQL из коллекции с помощью модели развертывания диспетчера ресурсов

1. Войдите на [портал Azure](https://portal.azure.com), используя свою учетную запись.
1. На портале Azure нажмите кнопку **+Создать**. Откроется колонка **Создать**. Шаблоны виртуальных VM SQL Server находятся на Marketplace в группе **Вычисления**.

1. В колонке **Создать** щелкните элемент **Вычисления**.
1. Чтобы просмотреть все типы ресурсов в колонке **Вычисления**, щелкните **Просмотреть все**. <br/> ![Колонка «Вычисления» в Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-blade.png) <br/>
1. В разделе **Серверы баз данных** щелкните элемент **SQL Server**, чтобы просмотреть все доступные шаблоны для SQL Server. Возможно, потребуется прокрутить страницу вниз, чтобы найти элемент **Серверы баз данных**.
1. 	Каждый шаблон определяет версию SQL Server и операционную систему. Выберите один из этих образов из списка, чтобы отобразилась колонка, содержащая сведения о нем.
1.	Колонка со сведениями содержит описание образа виртуальной машины. В этой колонке можно выбрать модель развертывания. В разделе **Выбор модели развертывания** выберите элемент **Диспетчер ресурсов** и нажмите кнопку **Создать**. <br/> ![Колонка «Вычисления» в Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> Настройка VM
На портале Azure есть пять колонок для настройки виртуальной машины SQL Server.

1.	Настройка основных параметров.
1.	Выбор размера виртуальной машины.
1.	Настройка параметров виртуальной машины.
1.	Настройка SQL Server.
1.	Просмотр сводки.

## 1\. Настройка основных параметров
В колонке **Создание виртуальной машины** в разделе **Основные сведения** укажите следующую информацию.

* Уникальное **имя** виртуальной машины.
* В окне **Имя пользователя** введите уникальное имя пользователя учетной записи локального администратора компьютера. Эта учетная запись будет также членом фиксированной серверной роли sysadmin SQL Server.
* В поле **Пароль** введите надежный пароль.
* Если у вас несколько подписок, проверьте, правильно ли выбрана подписка для виртуальной машины, которую вы собираетесь создать.
* В поле **Группа ресурсов** введите имя для группы ресурсов. Чтобы использовать существующую группу ресурсов, щелкните **Выбрать существующую**. Группа ресурсов — это коллекция связанных служб в Azure. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md). Убедитесь, что выбрано **расположение**, соответствующее вашим требованиям.
* Нажмите кнопку **ОК**, чтобы сохранить параметры. <br/>

>![Основные сведения об SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-basic.png) <br/>

## 2\. Выбор размера виртуальной машины
В колонке **Создание виртуальной машины** в разделе **Размер** выберите размер виртуальной машины. На портале Azure отобразятся рекомендуемые размеры. Дополнительную информацию о размерах виртуальных машин см. в статье [Размеры виртуальных машин](virtual-machines-size-specs.md). Размеры зависят от выбранного шаблона. Размер определяет ежемесячные затраты на работу виртуальной машины. Выберите размер VM для сервера. Описание размеров VM SQL Server см. в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-performance-best-practices.md).

## 3\. Настройка параметров виртуальной машины
В колонке **Создание виртуальной машины** в разделе **Параметры** настройте службу хранилища Azure, сеть и мониторинг для виртуальной машины.

- В разделе **Служба хранилища** укажите тип диска. Для производственных рабочих нагрузок рекомендуется хранилище класса Premium.

>[AZURE.NOTE] Хранилище класса Premium включено по умолчанию. Поэтому размер машины автоматически меняется до размера, который поддерживает хранилище класса Premium. Если отключить параметр «Хранилище класса Premium», будет использоваться размер машины, который вы выбрали ранее.

- В разделе **Учетная запись хранения** можно принять автоматически подготовленное имя учетной записи хранения или щелкнуть элемент **Учетная запись хранения**, чтобы выбрать существующую учетную запись и настроить тип учетной записи хранения. По умолчанию Azure создает новую учетную запись хранения с локально избыточным хранилищем.

- В разделе **Сеть** можно принять автоматически заполненные значения для компонентов или щелкнуть каждый из них, чтобы настроить **виртуальную сеть**, **подсеть**, **общедоступный IP-адрес** и **группу безопасности сети**. По умолчанию Azure автоматически настраивает эти значения.

- Azure по умолчанию включает **мониторинг** с помощью той же учетной записи хранения, которая будет использоваться для виртуальной машины. Эти параметры можно изменить.

- В поле **Группа доступности** укажите группу доступности. Для целей этого руководства можно выбрать значение **none**. Если вы планируете настроить группы доступности AlwaysOn SQL, настройте доступность, чтобы не создавать виртуальную машину повторно. Дополнительные сведения см. в статье [Управление доступностью виртуальных машин](virtual-machines-manage-availability.md).

## 4\. Настройка SQL Server
В колонке **Создание виртуальной машины** в разделе **Настройка SQL Server** настройте конкретные параметры и методы оптимизации SQL Server. Для SQL Server можно настроить следующие параметры:

- Соединение
- Аутентификация
- Оптимизация хранилища
- установка исправлений;
- Резервные копии
- Интеграция с хранилищем ключей

### Соединение
В разделе **Подключение SQL** выберите параметр **Общедоступное (Интернет)** для подключения к SQL Server с компьютеров или служб в Интернете. Если этот параметр выбран, Azure автоматически настроит брандмауэр и группу безопасности сети, чтобы разрешить трафик через порт 1433. <br/>![Подключение к SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-connectivity-alt.png) <br/>

Чтобы подключиться к SQL Server через Интернет, также необходимо включить проверку подлинности SQL Server.

>[AZURE.NOTE]В целях безопасности ограничьте исходный порт с помощью группы безопасности сети. Дополнительные сведения см. в статье [Группа безопасности сети](virtual-networks-nsg.md)

Если вы предпочитаете не включать автоматическое подключение к ядру СУБД через Интернет, выберите один из следующих параметров: 1) **Локальное (внутри VM)**, чтобы разрешить подключения к SQL Server только с виртуальной машины; 2) **Закрытое (в виртуальной сети)**, чтобы разрешить подключения к SQL Server с компьютеров или из служб в той же виртуальной сети.


Значение **порта** по умолчанию — 1433. Вы можете указать другой номер порта. Дополнительные сведения см. в статье [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).



### Аутентификация
Если требуется проверка подлинности SQL Server, щелкните **Включить** возле параметра **Проверка подлинности SQL**.

<br/>![Проверка подлинности SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-authentication.png) <br/>


Если проверка подлинности SQL Server включена, укажите **имя для входа** и **пароль**. Это имя пользователя будет именем для входа во время проверки подлинности SQL Server и членом фиксированной серверной роли sysadmin. Дополнительную информацию о режимах проверки подлинности см. в статье [Выбор режима проверки подлинности](http://msdn.microsoft.com/library/ms144284.aspx). По умолчанию SQL Server не поддерживает проверку подлинности SQL Server. В этом случае локальные администраторы на виртуальной машине могут подключаться к экземпляру SQL Server.

>[AZURE.NOTE] Если вы планируете предоставить доступ к SQL Server через Интернет (т. е. включаете параметр общедоступного подключения), включите проверку подлинности SQL здесь. Для общего доступа к SQL Server необходимо использовать проверку подлинности SQL.

### Оптимизация хранилища
Щелкните элемент **Конфигурация хранилища**, чтобы указать требования к хранилищу. Вы можете указать такие требования, как количество операций ввода-вывода в секунду (IOPs), пропускная способность в МБ/с и общий размер хранилища. Настройте эти параметры на скользящих шкалах. Портал автоматически вычисляет количество дисков на основании этих требований.

По умолчанию Azure оптимизирует хранилище для 5000 операций ввода-вывода, 200 МБ/с и 1 ТБ дискового пространства. Вы можете изменить эти параметры хранения в зависимости от рабочей нагрузки. В разделе **Оптимизация хранилища** выберите один из следующих параметров.

- **Общая** — значение по умолчанию, которое поддерживает большинство рабочих нагрузок.
- **Транзакционная** обработка оптимизирует хранилище для рабочих нагрузок OLTP традиционных баз данных.
- **Хранение данных** оптимизирует хранилище для рабочих нагрузок аналитики и создания отчетов.

На рисунке ниже показана колонка «Конфигурация хранилища». <br/>![Хранилище SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] Ограничения конфигурации хранилища зависят от размера виртуальной машины. Дополнительные сведения см. в статье [Размеры виртуальных машин](virtual-machines-size-specs.md).

### установка исправлений;
Параметр **Автоматическая установка исправлений SQL** включен по умолчанию. Автоматическая установка исправлений позволяет Azure автоматически исправлять SQL Server и операционную систему. Укажите день недели, время и длительность периода обслуживания. Azure будет выполнять исправления в период обслуживания. Расписание периода обслуживания использует для определения времени региональные параметры VM. Если вы не хотите, чтобы платформа Azure автоматически исправляла SQL Server и операционную систему, щелкните **Отключить**.

<br/>![Исправление SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-patching.png) <br/>

Дополнительную информацию см. в статье [Автоматическая установка исправлений для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-automated-patching.md).

### Резервные копии
Включите автоматическое резервное копирование для всех баз данных в разделе **Автоматическое резервное копирование SQL**. Во время включения автоматического резервного копирования SQL можно настроить следующие параметры:

- период удержания резервной копии в днях;
- учетная запись хранения, используемая для резервных копий;
- необходимость шифрования резервной копии. Для шифрования резервной копии щелкните **Включить**. Если автоматические резервные копии шифруются, укажите пароль. Azure создает сертификат для шифрования резервных копий и использует указанный пароль для защиты этого сертификата.

<br/>![Резервное копирование SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-autobackup.png) <br/>

 Дополнительную информацию см. в статье [Автоматическое резервное копирование для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-automated-backup.md).

### Интеграция с хранилищем ключей
Чтобы хранить конфиденциальные данные безопасности в Azure для шифрования, щелкните элемент **Интеграция с хранилищем ключей Azure** и нажмите кнопку **Включить**.

<br/>![Интеграция SQL ARM с хранилищем ключей Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-akv.png) <br/>

В следующей таблице перечислены параметры, необходимые для настройки интеграции с хранилищем ключей Azure.

|ПАРАМЕТР|ОПИСАНИЕ|ПРИМЕР|
|----------|----------|-------|
|**URL-адрес хранилища ключей** | Расположение хранилища ключей.|https://contosokeyvault.vault.azure.net/ |
|**Имя субъекта хранилища ключей Azure (AKV)** |Имя субъекта-службы Azure Active Directory. Этот имя также называется «Идентификатор клиента». |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Секрет субъекта хранилища ключей**|Интеграция AKV создает учетные данные в рамках SQL Server, с помощью которых VM будет получать доступ к хранилищу ключей. Выберите имя для этих учетных данных. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Имя учетных данных**|Выберите имя для этих учетных данных.| mycred1|

Дополнительные сведения см. в статье [Настройка интеграции с хранилищем ключей Azure для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-azure-key-vault-integration.md).

## 5\. Просмотр сводки
Просмотрите сводные данные и нажмите кнопку **ОК**, чтобы создать SQL Server, группу ресурсов и ресурсы, указанные для этой VM. Развертывание можно отслеживать на портале Azure. Если нажать кнопку **Уведомления** в верхней части окна, отобразятся основные данные состояния развертывания.

##<a id="Open"> Открытие виртуальной машины с помощью удаленного рабочего стола, а также выполнение полной установки
Выполните следующие действия, чтобы открыть виртуальную машину с помощью удаленного рабочего стола.

1.	После создания VM Azure на панели мониторинга Azure отобразится значок VM. Щелкните значок, чтобы просмотреть сведения о VM.
1.	В верхней части колонки VM щелкните **Подключиться**. Браузер загрузит RDP-файл для VM. Откройте RDP-файл.
1.	Подключение к удаленному рабочему столу сообщит, что издателя этого удаленного подключения невозможно определить, а затем спросит, хотите ли вы подключиться. Щелкните **Подключить**.
1.	В диалоговом окне **Безопасность Windows** выберите **Другая учетная запись**. В поле **Имя пользователя** введите <machine name><имя\_пользователя>, которое вы указали во время настройки VM.

После подключения к виртуальной машине SQL Server вы можете запустить SQL Server Management Studio и подключиться с проверкой подлинности Windows, используя свои учетные данные локального администратора. Также вы сможете изменить параметры брандмауэра или параметров конфигурации SQL Server после подготовки.

##<a id="Connect"> Подключение к SQL Server через Интернет

Если вы хотите подключиться к ядру СУБД SQL Server из Интернета, нужно выполнить несколько необходимых действий, таких как настройка брандмауэра, включение проверки подлинности SQL Server и настройка группы безопасности сети. Вы должны настроить правило группы безопасности сети, которое разрешает трафик TCP на порту 1433.

Если вы используете портал для подготовки образа виртуальной машины SQL Server с помощью диспетчера ресурсов, эти действия будут выполнены автоматически, когда вы выберете вариант подключения SQL **Общедоступное** и включите проверку подлинности SQL Server. Но остается еще несколько действий, которые нужно выполнить для получения доступа к экземпляру SQL Server через Интернет.

>[AZURE.NOTE] Если вы не выбрали параметр «Общедоступное» во время подготовки, для доступа к экземпляру SQL Server через Интернет потребуется выполнить дополнительные действия. Дополнительные сведения см. в статье [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).

Эти действия не нужно выполнять, если требуется только локальный доступ к виртуальной машине или доступ в пределах той же виртуальной сети.

> [AZURE.INCLUDE [Подключение к SQL Server в диспетчере ресурсов VM](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next"> Дальнейшие действия
Дополнительные сведения об использовании SQL Server в Azure см. в статье [Общие сведения об SQL Server на виртуальных машинах Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->