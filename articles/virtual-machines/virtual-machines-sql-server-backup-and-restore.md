<properties
	pageTitle="Резервное копирование и восстановление для SQL Server | Microsoft Azure"
	description="Содержит сведения об архивации и восстановлении баз данных SQL Server на виртуальных машинах Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Резервное копирование и восстановление SQL Server в виртуальных машинах Azure

## Общие сведения

Архивация данных в базах данных SQL Server является важной частью стратегии защиты от потери данных из-за ошибки приложения или пользователя. Это относится и к SQL Server на виртуальных машинах Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Для SQL Server на виртуальных машинах Azure можно применять собственные методы архивации и восстановления, используя для хранения файлов резервных копий подключенные диски. При этом существует ограничение на количество дисков, которые можно присоединить к виртуальной машине Azure, связанное с [размером виртуальной машины](virtual-machines-size-specs.md). Также необходимо учитывать накладные расходы на управление дисками.

Начиная с SQL Server 2014 для архивации и восстановления можно использовать хранилище больших двоичных объектов Microsoft Azure. Дополнительные возможности для этого параметра реализуются также в версии SQL Server 2016. Кроме того, SQL Server 2016 позволяет практически мгновенно создавать резервные копии файлов базы данных, которые находятся в хранилище больших двоичных объектов Microsoft Azure, и быстро их восстанавливать с помощью моментальных снимков Azure. В этой статье содержится обзор этих параметров. Дополнительные сведения см. в статье [Архивация и восстановление SQL Server с помощью службы хранилища больших двоичных объектов Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Обсуждение параметров архивации очень больших баз данных см. в статье [Стратегии архивации терабайтных баз данных SQL Server для виртуальных машин Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

В следующих разделах приводятся сведения по отдельным версиям SQL Server, которые поддерживаются виртуальными машинами Azure.

## Особенности архивации при хранении файлов в службе хранилища больших двоичных объектов Microsoft Azure

Если файлы базы данных хранятся в хранилище больших двоичных объектов Microsoft Azure, основания для архивации базы данных и сама технология архивации меняются. Дополнительные сведения о хранении файлов базы данных в хранилище больших двоичных объектов Azure см. в статье [Файлы данных SQL Server в Azure](https://msdn.microsoft.com/library/jj919148.aspx).

- Архивировать базы данных для защиты от сбоев оборудования или отказа носителей больше не нужно, поскольку такая защита предоставляется в рамках службы Microsoft Azure по умолчанию.

- Архивация базы данных требуется для защиты от ошибок пользователей, а также для формирования архивов, соблюдения нормативных требований или в административных целях.

- Функция архивации моментальных снимков файлов SQL Server в Microsoft SQL Server 2016 CTP-версии 3 (CTP3) позволяет мгновенно создавать резервные копии и выполнять восстановление. Дополнительные сведения см. в статье [Архивация моментальных снимков файлов базы данных в Azure](https://msdn.microsoft.com/library/mt169363.aspx).

## Архивация и восстановление в Microsoft SQL Server 2016 CTP-версии 3 (CTP3)

Microsoft SQL Server 2016 CTP-версии 3 (CTP3) поддерживает функции [архивации и восстановления с использованием больших двоичных объектов Azure](https://msdn.microsoft.com/library/jj919148.aspx), реализованные в SQL Server 2014. Эти функции описаны ниже. Однако эта версия включает также следующие усовершенствования:

- **Чередование**: при создании резервных копий в хранилище больших двоичных объектов Microsoft Azure SQL Server 2016 позволяет использовать сразу несколько больших двоичных объектов и, таким образом, архивировать большие базы данных размером до 12,8 ТБ.

- **Архивация моментальных снимков**: используя моментальные снимки Azure, функция архивации моментальных снимков файлов SQL Server дает возможность практически мгновенно создавать резервные копии и быстро восстанавливать файлы баз данных, сохраненные с помощью службы хранилища больших двоичных объектов. Это позволяет упростить политики архивации и восстановления политик. Функция архивации моментальных снимков файлов поддерживает также восстановление до определенной точки во времени. Дополнительные сведения см. в статье [Резервные копии для снимков файлов баз данных в Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).

- **Планирование управляемого резервного копирования**: функция управляемого резервного копирования SQL Server в Azure теперь поддерживает пользовательские расписания. Дополнительные сведения см. в статье [Управляемое резервное копирование SQL Server в Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).

>[AZURE.NOTE] Инструкции по использованию функций SQL Server 2016 при работе с хранилищем больших двоичных объектов Azure см. в учебнике [Использование службы хранилища больших двоичных объектов Microsoft Azure с базами данных SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Архивация и восстановление в SQL Server 2014

SQL Server 2014 включает следующие усовершенствования:

1. **Резервное копирование и восстановление в Azure**.

 - Функция *резервного копирование SQL Server с использованием указанного URL-адреса хранилища* теперь поддерживается в среде SQL Server Management Studio. Теперь при выполнении задачи архивации или восстановления, а также при работе с мастером планов обслуживания в SQL Server Management Studio можно сохранять резервные копии в Azure. Дополнительные сведения см. в статье [Резервное копирование SQL Server с использованием указанного URL-адреса хранилища](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - В функцию *управляемого резервного копирования SQL Server в Azure* добавлены новые возможности, позволяющие автоматизировать управление резервным копированием. Они пригодятся при управлении автоматической архивацией для экземпляров SQL Server 2014, выполняемых на машине Azure. Дополнительные сведения см. в статье [Управляемое резервное копирование SQL Server в Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - Функция *автоматического резервного копирования* позволяет автоматически включать *управляемое резервное копирование SQL Server в Azure* во всех действующих и новых базах данных для виртуальной машины SQL Server в Azure. Дополнительную информацию см. в статье [Автоматическое резервное копирование для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-automated-backup.md).
 - Общие сведения обо всех параметрах резервного копирования SQL Server 2014 в Azure см. в статье [Резервное копирование и восстановление SQL Server с помощью службы хранилища больших двоичных объектов Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Шифрование**: SQL Server 2014 поддерживает шифрование данных при создании резервной копии. Поддерживаются несколько алгоритмов шифрования и использование сертификата или асимметричного ключа. Дополнительные сведения см. в статье [Шифрование резервных копий](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## Архивация и восстановление в SQL Server 2012

Подробные сведения о резервном копировании и восстановлении в SQL Server 2012 см. в статье [Резервное копирование и восстановление баз данных SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Начиная с SQL Server 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 2, поддерживается архивация и восстановление через службу хранилища больших двоичных объектов Azure. Это улучшение можно использовать для архивации баз данных SQL Server в SQL Server на виртуальной машине Azure или в локальном экземпляре. Дополнительные сведения см. в статье [Резервное копирование и восстановление SQL Server с помощью службы хранилища больших двоичных объектов Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

В число преимуществ, связанных с использованием службы хранилища больших двоичных объектов Azure, входят возможность обхода ограничения, позволяющего подключать не больше 16 дисков, легкость управления, а также прямая доступность резервных файлов для другого экземпляра SQL Server, работающего на виртуальной машине Azure, или локальных экземпляров для переноса или аварийного восстановления данных. Полный список преимуществ, связанных с хранением резервных копий SQL Server в службе хранилища BLOB-объектов Azure, см. в статье [Резервное копирование и восстановление SQL Server с помощью службы хранилища больших двоичных объектов Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx) в разделе *Преимущества*.

Рекомендации и сведения об устранении неполадок см. в статье [Рекомендованные методы резервного копирования и восстановления (служба хранилищ больших двоичных объектов Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## Архивация и восстановление в других версиях SQL Server, поддерживаемые на виртуальной машине Azure

Информацию о резервном копировании и восстановлении в SQL Server 2008 R2 см. в статье [Резервное копирование и восстановление баз данных в SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Информацию о резервном копировании и восстановлении в SQL Server 2008 см. в статье [Архивация и восстановление баз данных в SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## Дальнейшие действия

Планируя развертывание SQL Server на виртуальной машине Azure, воспользуйтесь рекомендациями из учебника [Подготовка виртуальной машины SQL Server в Azure с помощью диспетчера ресурсов Azure](virtual-machines-sql-server-provision-resource-manager.md).

При том, что архивацию и восстановление можно использовать для переноса данных, существуют более простые способы переноса данных в SQL Server на виртуальной машине Azure. Полное описание вариантов переноса и соответствующие рекомендации см. в статье [Перенос базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

Просмотрите и другие [ресурсы по запуску SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0204_2016-->