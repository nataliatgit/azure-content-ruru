<properties
   pageTitle="Использование динамических DNS для регистрации имен узлов"
   description="На этой странице приведены некоторые сведения о настройке динамических DNS для регистрации имен узлов на собственных DNS-серверах."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Использование динамических DNS для регистрации имен узлов на собственном DNS-сервере
[Azure предоставляет разрешение имен](virtual-networks-name-resolution-for-vms-and-role-instances.md) для виртуальных машин и экземпляров ролей. Однако, если разрешение имен должно выходить за рамки возможностей Azure, вы можете предоставить свои DNS-серверы. В этом случае ваше решение DNS можно настроить в соответствии с конкретными потребностями. Например, вам может понадобиться доступ к локальным ресурсам через контроллер домена Active Directory.

Если пользовательские DNS-серверы размещены как виртуальные машины Azure, вы можете направлять запросы имени узла (в той же виртуальной сети) в Azure для разрешения внутренних IP-адресов. Если вы не хотите использовать этот маршрут, то можете зарегистрировать свои виртуальные машины на DNS-сервере с помощью динамической службы DNS. Azure не может (не имеет учетных данных) регистрировать записи непосредственно на DNS-серверах, поэтому часто требуется прибегать к альтернативным вариантам. Ниже приведены некоторые распространенные сценарии и альтернативные варианты.

## Клиенты Windows
Не входящие в домен клиенты Windows пытаются выполнить небезопасные обновления динамических DNS (DDNS) при загрузке или изменении своего IP-адреса. DNS-именем является имя узла и основной DNS-суффикс. Azure оставляет основной DNS-суффикс пустым, но вы можете предопределить это действие на виртуальной машине с помощью [пользовательского интерфейса](https://technet.microsoft.com/library/cc794784.aspx) или [службы автоматизации](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Входящие в домен клиенты Windows регистрируют свои IP-адреса на контроллере домена, используя защищенные динамические DNS. Процесс присоединения к домену задает основной DNS-суффикс на стороне клиента и управляет отношением доверия.

## Клиенты Linux
Клиенты Linux обычно не регистрируются на DNS-сервере при запуске — предполагается, что это делает DHCP-сервер. DHCP-серверы Azure не имеют возможности регистрировать записи на вашем DNS-сервере. Для отправки обновлений динамических DNS можно использовать средство *nsupdate*, которое входит в пакет Bind. Так как протокол динамических DNS является стандартизованным, средство *nsupdate* можно применять даже в том случае, если Bind не используется на DNS-сервере.

Для регистрации имени узла на DNS-сервере можно использовать обработчики, предоставляемые DHCP-клиентом. Во время цикла DHCP клиент выполняет скрипты в расположении */etc/dhcp/dhclient-exit-hooks.d/*. Это можно использовать для регистрации нового IP-адреса с помощью *nsupdate*. Например:

    	#!/bin/sh
    	requireddomain=mydomain.local

    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi

		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

Для выполнения безопасных обновлений динамических DNS можно также использовать команду *nsupdate*. Например, при использовании DNS-сервера Bind [создается](http://linux.yyz.us/nsupdate/) пара открытого и закрытого ключей. DNS-сервер [настраивается](http://linux.yyz.us/dns/ddns-server.html) с использованием открытой части ключа, что делает возможным проверку подписи запроса. Чтобы подписать запрос на обновление динамических DNS, в средстве *nsupdate* необходимо указать пару ключей с помощью параметра *-k*.

При использовании DNS-сервера Windows в *nsupdate* можно выполнять проверку подлинности Kerberos с параметром *-g* (недоступно в версии средства *nsupdate* для Windows). Для этого используйте команду *kinit*, чтобы скачать учетные данные (например, из [файла keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Затем выполните команду *nsupdate -g*, чтобы получить учетные данные из кэша.

При необходимости в виртуальные машины можно добавить суффикс поиска DNS. DNS-суффикс указан в файле */etc/resolv.conf*. Большинство дистрибутивов Linux управляет содержимым этого файла автоматически, поэтому его обычно нельзя изменить. Тем не менее, вы можете переопределить суффикс с помощью команды *supersede* DHCP-клиента. Для этого в файле */etc/dhcp/dhclient.conf* добавьте:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0224_2016-->