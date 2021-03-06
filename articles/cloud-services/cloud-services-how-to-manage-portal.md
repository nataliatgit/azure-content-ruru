<properties 
	pageTitle="Общие задачи управления облачными службами | Microsoft Azure" 
	description="Узнайте, как управлять облачными службами с помощью портала Azure. В этих примерах используется портал Azure." 
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
	ms.date="01/20/2016"
	ms.author="adegeo"/>


# Управление облачными службами

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-manage.md)
- [Azure portal](cloud-services-how-to-manage-portal.md)

В области **Облачные службы** портала Azure можно обновить роль службы или развертывание, перенести промежуточное развертывание в рабочую среду, привязать ресурсы к облачной службе для просмотра их зависимостей и масштабирования, а также удалить облачную службу или развертывание.


## Практическое руководство. Обновление роли облачной службы или развертывания

Если необходимо обновить код приложения для облачной службы, нажмите **Обновление** в колонке облачной службы. Одновременно можно обновить как одну, так и все роли. При этом также потребуется загрузить новые файлы пакета и конфигурации службы.

1. На [портале Azure][] выберите облачную службу, которую требуется обновить. Откроется колонка экземпляра облачной службы.

2. В колонке нажмите кнопку**Обновить**.

    ![Кнопка «Обновить»](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Обновите развертывание с новым файлом пакета службы (.cspkg) и файлом конфигурации службы (.cscfg).

    ![Обновление развертывания](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **При необходимости** обновите метку развертывания и учетную запись хранения.

5. Если в процессе обновления произошло изменение числа или размера ролей, установите флажок **Разрешить обновление при изменении размера или числа ролей**, чтобы выполнить обновление.

	>[AZURE.WARNING] Обратите внимание, что при изменении размера роли (то есть размера виртуальной машины, на которой размещается экземпляр) или числа ролей необходимо повторно установить образ для каждого экземпляра роли (виртуальная машина), в результате чего утрачиваются все локальные данные.

6. Если в облачной службе будут содержаться роли с одним экземпляром, установите флажок **Обновить, даже если одна или несколько ролей содержат один экземпляр**, чтобы обеспечить обновление.

	Служба Azure гарантирует доступность облачной службы в течение 99,95 % времени, только если для каждой роли определены как минимум два экземпляра (виртуальные машины). Такая конфигурация позволяет обрабатывать запросы клиентов на одной виртуальной машине во время обновления другой.

8. Нажмите **ОК**, чтобы начать обновление службы.



## Практическое руководство. Переключение из промежуточного в рабочее развертывание

Чтобы перенести развертывание облачной службы из промежуточной в рабочую среду, воспользуйтесь командой **Переключить**. Если вы решили развернуть новый выпуск облачной службы, вы можете протестировать его в промежуточном развертывании, а ваши клиенты будут и дальше использовать текущий рабочий выпуск. Когда вы будете готовы к развертыванию нового выпуска в рабочей среде, используйте команду **Переключить** для переключения URL-адресов двух развертываний.

Соответствующая команда представлена на странице **Облачные службы** или на панели мониторинга.

1. На [портале Azure][] выберите облачную службу, которую требуется обновить. Откроется колонка экземпляра облачной службы.

2. В колонке нажмите кнопку**Заменить**.

    ![Переключение облачных служб](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Откроется следующий запрос подтверждения.

	![Переключение облачных служб](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Проверьте данные развертывания и нажмите кнопку **ОК**, чтобы заменить развертывание.

	Переключение осуществляется мгновенно, поскольку при этом изменяются только виртуальные IP-адреса развертываний.

	Для экономии вычислительных ресурсов вы можете удалить промежуточную среду после того, как убедитесь в соответствии рабочего развертывания заданным характеристикам.

## Практическое руководство. Привязка ресурсов к облачной службе

Портал Azure не связывает ресурсы вместе в отличие от классического портала Azure. Вместо этого необходимо развертывать дополнительные ресурсы в ту же группу ресурсов, которая используется облачной службой.

## Практическое руководство. Удаление развертываний и облачной службы

До удаления облачной службы необходимо удалить все текущие развертывания.

Для экономии вычислительных ресурсов вы можете удалить промежуточную среду после того, как убедитесь в соответствии рабочего развертывания заданным характеристикам. Обратите внимание, что плата за вычислительные ресурсы для экземпляра роли взимается даже в том случае, если облачная служба не выполняется.

Чтобы удалить развертывание или облачную службу, выполните следующие действия.

1. На [портале Azure][] выберите облачную службу, которую требуется удалить. Откроется колонка экземпляра облачной службы.

2. В колонке нажмите кнопку**Удалить**.

    ![Переключение облачных служб](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Можно удалить все облачную службу, выбрав **Облачная служба и ее развертывания**, или выбрать **Рабочее развертывание** или **Промежуточное развертывание**.

    ![Переключение облачных служб](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Нажмите кнопку **Удалить** внизу.

5. Чтобы удалить службу, щелкните **Удалить облачную службу**. Нажмите кнопку **Да** в запросе подтверждения.

> [AZURE.NOTE]
Если для удаляемой облачной службы был настроен подробный мониторинг, платформа Azure не удаляет данные мониторинга из учетной записи хранения. В этом случае требуется удалить такие данные вручную. Дополнительные сведения о поиске таблиц метрик см. в [этой](cloud-services-how-to-monitor.md) статье.

[портале Azure]: https://portal.azure.com

## Дальнейшие действия

* [Общая настройка облачной службы](cloud-services-how-to-configure-portal.md).
* Узнайте, как [развернуть облачную службу](cloud-services-how-to-create-deploy-portal.md).
* Настройте [пользовательское доменное имя](cloud-services-custom-domain-name-portal.md).
* Настройка [SSL-сертификатов](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0128_2016-->