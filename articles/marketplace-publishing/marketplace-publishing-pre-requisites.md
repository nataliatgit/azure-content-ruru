<properties
   pageTitle="Нетехнические предварительные требования, необходимые для создания предложения в Azure Marketplace | Microsoft Azure"
   description="Ознакомьтесь с требованиями по созданию и развертыванию предложения Azure Marketplace, предназначенного для продажи другим пользователям."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="01/07/2016"
  ms.author="hascipio; v-divte"/>

# Общие предварительные требования для создания предложения в Azure Marketplace
Эта статья содержит общие предварительные относящиеся к бизнес-процессу требования, которые необходимы для создания предложения.

## Убедитесь, что у вас есть учетная запись продавца Майкрософт
Подробные инструкции по регистрации учетной записи продавца Майкрософт см. в статье [Создание и регистрация учетной записи](marketplace-publishing-accounts-creation-registration.md).

- Если учетная запись **уже зарегистрирована**, узнайте, кому в вашей компании она принадлежит или какие учетные данные были использованы для регистрации.
- Если вы **не являетесь владельцем учетной записи публикации**, владелец учетной записи может добавить вашу учетную запись Майкрософт в качестве соадминистратора на [портал публикации](https://publish.windowsazure.com). На вкладке **Издатели** щелкните ссылку **Администраторы**.
- Убедитесь, что заинтересованные лица в процессе публикации Azure получают электронные письма, отправляемые на этот адрес. Для завершения процесса публикации эти письма необходимо отслеживать и отвечать на них.
- Не создавайте учетную запись, связанную с определенным человеком. Если этот человек уйдет из вашей организации, он сможет повлиять на доступ к сведениям об SKU и возможность публикации ваших SKU.

> [AZURE.IMPORTANT] Если вы собираетесь публиковать только бесплатные предложения (или предложения, использующие собственную лицензию), заполнять банковскую и налоговую информацию о компании не нужно.

> Для начала работы необходимо выполнить регистрацию компании. Однако пока компания работает над налоговой и банковской информацией в учетной записи центра разработчиков Майкрософт, разработчики могут начать работу по созданию образа виртуальной машины в [портале публикации](https://publish.windowsazure.com), а также по его сертификации и тестированию в промежуточной среде Azure. Полное утверждение учетной записи продавца необходимо только для последнего этапа публикации вашего предложения в Azure Marketplace.

> При возникновении проблем с регистрацией продавца отправьте запрос в службу поддержки следующим образом. 1. Обратитесь в [службу поддержки](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=ru-RU&pesid=15635&ccsid=635847950577064286). 2. Выберите **Центр разработчиков**. 3. Выберите **Профиль издателя**. 4. Выберите способ связи.


## Получение подписки Azure с оплатой по мере использования
Эта подписка будет использоваться для создания образов виртуальных машин и передачи образов в [Azure Marketplace](https://azure.microsoft.com/marketplace/). Если у вас нет существующей подписки, войдите в систему здесь https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## Допустимые страны продаж
> [AZURE.WARNING]
Для продажи своих услуг в Azure Marketplace ваше зарегистрированное юридическое лицо должно быть из одной из утвержденных "стран продаж". Это ограничение связано с выплатами и налогообложением. Мы активно стараемся расширить этот список стран, поэтому следите за новостями. Полный список см. в разделе 1b [политик участия в Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## Дальнейшие действия
Далее приводятся предварительные технические требования для каждого типа предложения. Щелкните ссылку на статью для соответствующего типа предложения, который вы хотите использовать в Azure Marketplace.

| Образ виртуальной машины | Служба разработчика | Служба данных | Шаблон решения |
|-----|-----|-----|-----|
| [Предварительные технические требования для виртуальной машины](marketplace-publishing-vm-image-creation-prerequisites.md) | Предварительные технические требования для службы разработчика | [Предварительные технические требования для службы данных](marketplace-publishing-data-service-creation-prerequisites.md) | [Предварительные технические требования для шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md) |

## См. также
- [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0128_2016-->