<properties
	pageTitle="Вход в виртуальную машину под управлением Windows | Microsoft Azure"
	description="Использование портала Azure для входа в виртуальную машину Windows, созданную с использованием классической модели развертывания диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Вход в виртуальную машину под управлением Windows с помощью портала Azure


На портале Azure для запуска сеанса удаленного рабочего стола и входа в виртуальную машину Windows используйте кнопку **Подключиться**.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).


## Подключение к виртуальной машине

1. Войдите на портал Azure.

2. Щелкните **Виртуальные машины** и выберите виртуальную машину.

3. На панели команд в нижней части страницы щелкните **Подключиться**.

	![Вход на виртуальную машину](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Щелкните кнопку **Подключить**, чтобы создать и скачать файл протокола удаленного рабочего стола (RDP-файл). Чтобы воспользоваться этим файлом, щелкните **Открыть**.

	![Вход на виртуальную машину](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. Чтобы продолжить, в окне удаленного рабочего стола нажмите кнопку **Подключить**.

	![Продолжайте подключение](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. В окне безопасности Windows введите учетные данные для учетной записи виртуальной машины, а затем нажмите кнопку **ОК**.

 	В большинстве случаев учетные данные — это имя пользователя и пароль, заданные при создании виртуальной машины. Домен представляет собой имя виртуальной машины, введенное в формате *имя\_виртуальной\_машины*&#92;*имя\_пользователя*.
	
	Если виртуальная машина принадлежит домену в организации, убедитесь, что имя пользователя содержит имя этого домена в формате *домен*&#92;*имя\_пользователя*. Учетная запись также должна являться членом группы "Администраторы", либо ей должны быть назначены права удаленного доступа к виртуальной машине.
	
	Если виртуальная машина является контроллером домена, введите имя пользователя и пароль учетной записи администратора домена для данного домена.

7.	Щелкните **Да** для проверки удостоверения виртуальной машины и завершения входа в систему.

	![Проверьте удостоверение машины](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Дальнейшие действия

Если возникла ошибка подключения, см. статью [Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-troubleshoot-remote-desktop-connections.md).

<!---HONumber=AcomDC_0211_2016-->