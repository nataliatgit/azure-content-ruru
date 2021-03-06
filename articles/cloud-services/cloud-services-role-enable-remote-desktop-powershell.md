<properties 
pageTitle="Включение подключения к удаленному рабочему столу для роли в облачных службах Azure с помощью PowerShell" 
description="Как настроить приложение облачной службы Azure для подключений к удаленному рабочему столу с помощью PowerShell" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="01/19/2016" 
ms.author="saurabh"/>

# Включение подключения к удаленному рабочему столу для роли в облачных службах Azure с помощью PowerShell

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


С помощью удаленного рабочего стола обеспечивается доступ к рабочему столу экземпляра, работающего в Azure. Подключение к удаленному рабочему столу позволяет диагностировать и устранять неполадки выполняющегося приложения.

В этой статье описывается включение удаленного рабочего стола для ролей облачной службы с помощью PowerShell. Сведения о компонентах, которые потребуются для выполнения инструкций в этой статье, см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). В PowerShell используется подход с расширением удаленного рабочего стола, поэтому удаленный рабочий стол можно включить даже после развертывания приложения.


## Настройка удаленного рабочего стола с помощью PowerShell

Командлет [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) позволяет включить удаленный рабочий стол для всех или некоторых ролей в развернутой облачной службе. Он позволяет указать имя пользователя и пароль для пользователя удаленного рабочего стола с помощью параметра *Credential*, который принимает объект PSCredential.

Если вы используете PowerShell интерактивно, то можете задать объект PSCredential, вызвав командлет [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx).

```
	$remoteusercredentials = Get-Credential
```

В открывшемся диалоговом окне вы сможете в защищенном режиме указать имя пользователя и пароль для удаленного пользователя.

Так как PowerShell используется в основном в сценариях автоматизации, объект PSCredential можно настроить так, чтобы участие пользователя не требовалось. Для этого необходимо задать надежный пароль. Для начала нужно придумать текстовый пароль и преобразовать его в защищенную строку с помощью командлета [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Далее следует преобразовать защищенную строку в зашифрованную стандартную строку, используя командлет [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). После этого вы можете сохранить зашифрованную стандартную строку в файл с помощью командлета [Set-Content](https://technet.microsoft.com/library/ee176959.aspx). При создании объекта PSCredential можно не указывать пароль вручную и не хранить его в виде обычного текста, а выполнять считывание из этого файла.

Для создания файла надежного пароля используйте эту команду PowerShell:

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

После того как вы создадите файл пароля (password.txt), вы сможете использовать только его, и вам не потребуется вводить пароль в виде обычного текста. При необходимости сменить пароль можно сгенерировать обновленный файл password.txt. Для этого нужно просто выполнить указанную выше команду PowerShell для нового пароля.

>[AZURE.IMPORTANT] При выборе пароля убедитесь, что соблюдены [требования к сложности](https://technet.microsoft.com/library/cc786468.aspx).

Чтобы создать объект учетных данных из файла с надежным паролем, необходимо считать содержимое файла и преобразовать его обратно в защищенную строку с помощью командлета [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Командлет [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) принимает не только параметр Credential, но и параметр *Expiration*, который указывает значение даты и времени истечения срока действия учетной записи пользователя. Вы можете либо задать статическое значение даты и времени, либо выбрать срок действия относительно текущей даты.

В этом примере показано, как установить расширение удаленного рабочего стола для облачной службы с помощью PowerShell:

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
При необходимости вы также можете указать слот развертывания и роли, для которых необходимо включить удаленный рабочий стол. Если эти параметры не указаны, командлет по умолчанию будет использовать рабочий слот развертывания и включит удаленный рабочий стол для всех ролей в рабочей среде.

Расширение удаленного рабочего стола привязывается к развернутой службе. В случае создания нового развертывания службы потребуется еще раз включить удаленный рабочий стол для этого развертывания. Если вы хотите, чтобы удаленный рабочий стол всегда включался для ваших развертываний, интегрируйте сценарии PowerShell для включения удаленного рабочего стола в рабочий процесс развертывания.


## Удаленный рабочий стол для экземпляра роли
Командлет [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) можно использовать для добавления удаленного рабочего стола для определенного экземпляра роли облачной службы. Вы можете использовать в этом командлете параметр *LocalPath*, чтобы загрузить RDP-файл локально, или параметр *Launch*, чтобы напрямую открыть диалоговое окно «Подключение к удаленному рабочему столу» для доступа к экземпляру роли облачной службы.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Убедитесь, что расширение удаленного рабочего стола включено в службе. 
Командлет [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) показывает, включен ли удаленный рабочий стол в развернутой службе. Он возвращает имя пользователя удаленного рабочего стола и роли, для которых включено расширение удаленного рабочего стола. Вы также можете указать слот развертывания (по умолчанию используется рабочий слот).

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Удаление расширения удаленного рабочего стола из службы 
Если вы уже включили расширение удаленного рабочего стола в развернутой службе и хотите обновить параметры удаленного рабочего стола, то вам потребуется сначала удалить расширение удаленного рабочего стола, а затем включить его повторно с новыми параметрами. Например, если вы хотите задать новый пароль для учетной записи удаленного пользователя или если истек срок действия учетной записи пользователя, нужно сначала удалить расширение, а затем добавить его снова с обновленным паролем или сроком действия. Это относится только к существующим развертываниям, в которых включено расширение удаленного рабочего стола. К новым развертываниям можно применить расширение напрямую.

Чтобы удалить расширение удаленного рабочего стола из развертывания службы, используйте командлет [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx). При необходимости можно также указать слот развертывания и роль, для которой необходимо удалить удаленный рабочий стол.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE] Параметр *UninstallConfiguration* удаляет все конфигурации расширения, примененные к службе. Конфигурации расширения связаны с конфигурацией службы. Для активации расширения в развернутой службе развертывание должно быть связано с конфигурацией расширения. Вызов командлета Remove без параметра *UninstallConfiguration* разорвет связь развертывания с конфигурацией расширения, что позволит эффективно удалить расширение из развернутой службы. Однако конфигурация расширения будет по-прежнему связана со службой. Чтобы полностью удалить конфигурацию расширения, выполните командлет Remove с параметром *UninstallConfiguration*.



## Дополнительные ресурсы

[Настройка облачных служб](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_0128_2016-->