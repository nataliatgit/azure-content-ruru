<properties
	pageTitle="Создание и развертывание облачной службы | Microsoft Azure"
	description="Узнайте, как создать и развернуть облачную службу с помощью функции ";Быстрое создание"; в Azure. В этих примерах используется портал Azure."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Создание и развертывание облачной службы

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)
- [Azure classic portal](cloud-services-how-to-create-deploy.md)

Портал Azure предоставляет два способа создания и развертывания облачной службы: *быстрое создание* и *настраиваемое создание*.

В этой статье описывается быстрое создание облачной службы с последующим развертыванием соответствующего ей пакета в Azure с помощью функции **Отправка**. При выборе этого способа на портале Azure отображаются все необходимые для работы ссылки. Чтобы одновременно выполнить развертывание создаваемой облачной службы, воспользуйтесь функцией Настраиваемое создание.

> [AZURE.NOTE] Если вы планируете опубликовать облачную службу из Visual Studio Team Services (VSTS), воспользуйтесь функцией «Быстрое создание», а затем настройте публикацию VSTS на странице «Быстрый запуск» Azure или на панели мониторинга. Дополнительные сведения см. в разделе [Непрерывная доставка в Azure с использованием Visual Studio Team Service][TFSTutorialForCloudService], а также в справке для страницы **Быстрый запуск**.

## Основные понятия
Для развертывания приложения в качестве облачной службы в Azure необходимы три компонента:

- **Определение службы**. Файл определения службы (CSDEF-файл), задающий модель службы и некоторое количество ролей.

- **Конфигурация службы**. Файл конфигурации службы (CSCFG-файл), предоставляющий параметры конфигурации для облачной службы и отдельных ролей, включая число экземпляров ролей.

- **Пакет службы**. Пакет службы (с расширением CSPKG) содержит код и конфигурации приложений, а также файл определения службы.

Дополнительную информацию об этих компонентах и создании пакета см. [здесь](cloud-services-model-and-package.md).

## Подготовка приложения
Перед развертыванием облачной службы необходимо создать пакет облачной службы (CSPKG-файл) на основе кода приложения и файл конфигурации облачной службы (CSCFG). Инструменты для подготовки необходимых файлов развертывания находятся в пакете SDK для Azure. Этот пакет можно установить со страницы [Загрузки Azure](https://azure.microsoft.com/downloads/) на языке, выбранном для разработки кода приложения.

Перед экспортом пакета службы необходимо отдельно настроить три компонента облачной службы:

- Если в развертываемой облачной службе будет применяться SSL-шифрование данных, [настройте приложение](cloud-services-configure-ssl-certificate-portal.md#modify) для поддержки SSL.

- Если будут использоваться подключения к удаленному рабочему столу для экземпляров роли, [настройте роли](cloud-services-role-enable-remote-desktop.md) для удаленного рабочего стола. Это можно сделать только на классическом портале.

- Чтобы включить подробный мониторинг для веб-службы, настройте для нее систему диагностики Azure. *Минимальный мониторинг* (по умолчанию) реализуется на основе счетчиков производительности основной операционной системы, собирающих данные для экземпляров ролей (виртуальные машины). *В рамках подробного мониторинга* для более тщательного анализа проблем обработки приложений отслеживаются дополнительные метрики в экземплярах ролей. Дополнительную информацию о включении диагностики в Azure см. в разделе [Включение диагностики в Azure](cloud-services-dotnet-diagnostics.md).

Чтобы создать облачную службу с развертыванием веб-ролей или рабочих ролей, необходимо [создать соответствующий пакет службы](cloud-services-model-and-package.md#servicepackagecspkg).

## Перед началом работы

- Если пакет SDK для Azure не установлен, щелкните **Установить пакет Azure SDK**. Откроется страница [Загрузки Azure](https://azure.microsoft.com/downloads/), откуда можно скачать пакет SDK для языка, выбранного для разработки кода приложения. (Также это можно сделать позднее.)

- Для экземпляров роли с сертификатами создайте сертификаты. В облачных службах используется PFX-файл с закрытым ключом. [Сертификаты можно отправить в Azure]() как при создании, так и при развертывании облачной службы.

- Если планируется развертывание облачной службы в территориальной группе, создайте соответствующую группу. Территориальные группы предназначены для развертывания облачной и других служб Azure в одном расположении региона. Чтобы создать такую группу, откройте область **Сети** классического портала Azure и перейдите на страницу **Территориальные группы**.


## Шаг 3. Создание облачной службы и загрузка пакета развертывания

1. Перейдите на [портал Azure][].
2. Щелкните **Создать > Вычисления**, затем прокрутите вниз и щелкните **Облачная служба**.

    ![Публикация облачной службы](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. В нижней части страницы сведений нажмите кнопку **Создать**.
4. В колонке новой **облачной службы** введите **DNS-имя**.
5. Создайте новую **группу ресурсов** или выберите существующую.
6. Выберите **расположение**.
7. Выберите **Пакет** и заполните обязательные поля в колонке **Загрузите пакет**.  

     Если какая-либо из ролей содержит отдельный экземпляр, убедитесь, что установлен флажок **Развернуть, даже если одна или несколько ролей содержат отдельный экземпляр**.

8. Убедитесь, что установлен флажок **Запустить развертывание**.
9. Нажмите кнопку **ОК**.

    ![Публикация облачной службы](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Загрузить сертификат

Если пакет развертывания был [настроен для использования сертификатов](cloud-services-configure-ssl-certificate-portal.md#modify), теперь можно передать сертификат.

1. Выберите **Сертификаты** и в колонке **Добавить сертификаты** выберите PFX-файл SSL-сертификата и укажите **Пароль** сертификата.
2. Щелкните **Присоединить сертификат** и нажмите кнопку **ОК** в колонке **Добавить сертификаты**.
3. Щелкните **Создать** в колонке **Облачная служба**. Когда развертывание получит статус **Готово** можно выполнять следующие шаги.

    ![Публикация облачной службы](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Проверка успешного завершения развертывания

1. Щелкните экземпляр облачной службы.

	В строке состояния должна отображаться информация о том, что служба **Запущена**.

2. В разделе **Основное** щелкните **URL-адрес сайта**, чтобы открыть облачную службу в веб-браузере.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## Дальнейшие действия

* [Общая настройка облачной службы](cloud-services-how-to-configure-portal.md).
* Настройка [пользовательского имени домена](cloud-services-custom-domain-name-portal.md).
* [Управление облачной службой](cloud-services-how-to-manage-portal.md).
* Настройка [SSL-сертификатов](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->