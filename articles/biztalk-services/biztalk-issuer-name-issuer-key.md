<properties 
	pageTitle="Имя издателя и ключ издателя в службах BizTalk | Microsoft Azure" 
	description="Узнайте, как извлечь имя и ключ издателя для Service Bus или управления доступом (ACS) в службах BizTalk. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="mandia"/>




# Службы BizTalk: имя и ключ издателя

В службах BizTalk в Azure используются имя и ключ издателя Service Bus, а также имя и ключ издателя службы Access Control. В частности:

Задача | Используемые имя и ключ издателя
--- | ---
Развертывание приложения из Visual Studio | Имя и ключ издателя для службы Access Control
Настройка портала служб BizTalk в Azure | Имя и ключ издателя для службы Access Control
Создание бизнес-ретрансляций со службами адаптера BizTalk в Visual Studio | Имя и ключ издателя шины обслуживания

В этом разделе перечислены шаги для извлечения имени и ключа издателя.

## Имя и ключ издателя для службы Access Control
Имя и ключ издателя для службы Access Control используются в следующих областях:

- Приложение службы BizTalk в Azure, созданное в среде Visual Studio: для успешного развертывания в Azure приложения службы BizTalk, созданного в среде Visual Studio, необходимо ввести имя и ключ издателя для службы Access Control. 
- Портал служб BizTalk Azure: при открытии портала служб BizTalk во время создания службы BizTalk имя и ключ издателя для службы Access Control автоматически регистрируются для развертываний с теми же значениями службы Access Control.

### Копирование и вставка имени и ключа издателя для службы Access Control

1. Перейдите на [классический портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В области навигации слева щелкните **Службы BizTalk**.
3. Выберите службу BizTalk. 
4. Выберите **Сведения о подключении** на панели задач. Отображается список сведений о пространстве имен управления доступом Access Control, издателе по умолчанию (имя издателя) и ключе по умолчанию (ключ издателя), которые можно скопировать в буфер обмена.  

Итого: имя издателя = издатель по умолчанию; ключ издателя = ключ по умолчанию.


Чтобы получить значения службы Access Control, можно также выбрать **Открыть портал управления ACS**:

1. Перейдите на [классический портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В области навигации слева щелкните **Службы BizTalk**.
3. Выберите службу BizTalk.
4. Нажмите кнопку "Сведения о подключении" и выберите **Открыть портал управления ACS**.
5. В разделе **Параметры службы** портала выберите **Удостоверения службы**. Здесь отображается удостоверение службы, которое является именем издателя для службы Access Control. Щелкните ссылку на удостоверение службы, чтобы просмотреть пароль, который является значением ключа издателя. Эти значения можно скопировать.<br/><br/> Например, в поле **Удостоверения службы** отображается «Владелец». "Владелец" — это имя издателя для службы Access Control. При щелчке ссылки "владелец" откроется поле **Пароль**. При щелчке ссылки "Пароль" будет отображено соответствующее значение. Это значение пароля является ключом издателя для службы Access Control.  

Итого: имя издателя = имя удостоверения службы; ключ издателя = значение пароля.

Чтобы извлечь значения службы Access Control, также можно выбрать **Active Directory** в области навигации слева.

> [AZURE.IMPORTANT] Если при создании пространства имен службы Access Control был выбран параметр **Active Directory**, удостоверение службы **не создается** автоматически. При подготовке службы BizTalk автоматически создаются пространство имен контроля доступа, удостоверение службы «Владелец» (имя издателя), пароль (ключ издателя) и симметричный ключ.<br /> В разделе [Инструкция по использованию службы управления ACS для настройки удостоверений службы](http://go.microsoft.com/fwlink/p/?LinkID=303942) представлены дополнительные данные об удостоверениях службы контроля доступа.


## Имя и ключ издателя Service Bus
Имя и ключ издателя шины обслуживания используются службами адаптера BizTalk. В проекте служб BizTalk в Visual Studio можно использовать службы адаптера BizTalk для подключения к локальной бизнес-системе. Для подключения создайте бизнес-ретранслятор и введите сведения о бизнес-системе. При этом также вводится имя и ключ издателя шины обслуживания.

### Чтобы извлечь имя и ключ издателя Service Bus

1. Перейдите на [классический портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. В левой области навигации щелкните **Шина обслуживания**.
3. Выберите пространство имен. На панели задач выберите **Сведения о подключении**. Появятся поля **Издатель по умолчанию** (имя издателя) и **Ключ по умолчанию** (ключ издателя). Эти значения можно скопировать.  

Итого:  
имя издателя = издатель по умолчанию;
ключ издателя = ключ по умолчанию.

## Далее
Дополнительные разделы по службам BizTalk в Azure:

-  [Установка пакета SDK служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Учебники по службам BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Как приступить к работе с пакетом SDK для служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Службы Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## См. также
-  [Инструкция по использованию службы управления ACS для настройки удостоверений службы](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Службы BizTalk: подготовка с использованием классического портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Службы BizTalk. Диаграмма состояния подготовки](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Службы BizTalk: вкладки «Панель мониторинга», «Монитор» и «Масштаб»](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Службы BizTalk: архивация и восстановление](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Службы BizTalk: регулирование](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=AcomDC_0302_2016-->