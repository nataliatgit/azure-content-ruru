<properties
   pageTitle="Передача подписки Azure | Microsoft Azure"
   description="Передача подписки Azure другому пользователю и часто задаваемые вопросы об этой процедуре."
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="12/21/2015"
   ms.author="curtand;kareni;ruchic"/>

# Передача подписки Azure

Вам:

- Требуется передавать право на выставление счетов в подписке Azure другому лицу?
- Нужно изменить учетную запись, используемую для регистрации в Azure? Возможно, вы использовали свою учетную запись Майкрософт, но вместо нее хотели использовать рабочую или учебную учетную запись?
- Нужно переместить подписку Azure из одного каталога в другой?
- Требуется объединить Azure и Office 365 в разных клиентах?

Теперь вы можете легко это сделать в центре учетных записей Microsoft Azure для подписок с оплатой по мере использования, а также подписок MSDN, Action Pack или BizSpark. Мы добавили возможность передачи подписки другому пользователю. Другими словами, теперь можно изменить администратора учетной записи в любой подписке с оплатой по мере использования либо подписке MSDN, Action Pack или BizSpark, независимо от страны, в которой вы работаете.

## Передача подписки Azure

1.  Войдите на страницу <https://account.windowsazure.com/Subscriptions>

2.  Выберите подписку для передачи

3.  Щелкните параметр **Передать подписку**.

    ![Вкладка «Подписки учетной записи Azure»](./media/billing-subscription-transfer/image1.png)

4.  Следуйте инструкциям на экране, чтобы указать получателя.

    ![Диалоговое окно «Передача подписки»](./media/billing-subscription-transfer/image2.PNG)

5.  Получатель автоматически получит сообщение электронной почты со ссылкой, по которой он может принять подписку.

    ![Сообщение электронной почты получателю о передаче подписки](./media/billing-subscription-transfer/image3.png)

6.  Получатель переходит по ссылке и следует инструкциям, в том числе вводит свои данные для оплаты.

    ![Веб-страница «Передача первой подписки»](./media/billing-subscription-transfer/image4.PNG)

    ![Веб-страница «Передача второй подписки»](./media/billing-subscription-transfer/image5.PNG)

7. Готово! Теперь подписка передана.

## Часто задаваемые вопросы

-   **Приводит ли передача подписки к появлению простоев в работе службы?**

    Это не влияет на работу службы. Во время передачи отменяется подписка, связанная с администратором учетной записи, и создается новая подписка, связанная с учетной записью получателя. При этом базовые службы Azure связываются с новой подпиской. Идентификатор подписки не меняется.

-   **Как использовать этот механизм для изменения каталога для подписки?**-   
    Подписка Azure создается в каталоге, к которому принадлежит администратор учетных записей. Таким образом, чтобы изменить каталог, просто перенесите подписку в учетную запись пользователя в целевом каталоге. Когда пользователь выполнит действия по приему передачи, подписка будет автоматически перемещена в целевой каталог.
   
-   **Если я беру на себя выставление счетов в подписке другой организации, будет ли эта организация продолжать иметь доступ к моим ресурсам?**

    Если подписка передается другому клиенту, пользователи, связанные с предыдущим клиентом, потеряют доступ к подписке. Даже если пользователь больше не является администратором или соадминистратором служб, он все еще может иметь доступ к подписке через другие механизмы обеспечения безопасности. К этим механизмам относятся:
    - Сертификаты управления, которые предоставляют пользователю права администратора для доступа к ресурсам подписки. Дополнительную информацию см. в статье [Создание и передача сертификата управления для Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).
    -	Ключи доступа к службам, например к хранилищу. Дополнительную информацию см. в разделе [Просмотр, копирование и повторное создание ключей доступа к хранилищу](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Учетные данные для удаленного доступа к службам, например Виртуальным машинам Azure.

    Это не полный список. Получателю следует рассмотреть возможность обновления всех секретных данных, связанных со службой, если он хочет ограничить доступ к своим ресурсам. Большинство ресурсов можно обновить следующим образом.

    1.   Перейдите на портал Azure: [*https://portal.azure.com*](https://portal.azure.com).

    2.    Нажмите кнопку «Просмотреть все» -&gt; «Все ресурсы»

    3.    Выберите ресурс. Откроется колонка ресурсов.

    4.    В колонке ресурсов щелкните **Параметры**. Здесь можно просматривать и обновлять существующие секретные данные.


-   **Если передать подписки в середине цикла выставления счетов, будет ли получатель оплачивать весь цикл выставления счетов?**

    Отправитель должен оплатить все использованные ресурсы, о которых сообщено до момента завершения передачи. Получатель отвечает за оплату используемых ресурсов начиная со времени передачи. Возможно, некоторые ресурсы были использованы до передачи, но об этом было сообщено после нее. Эти ресурсы будут включены в счет получателя.

-   **Имеет ли получатель доступ к журналу использования и выставленным счетам?**

    В настоящее время получателю доступна только сумма последнего счета (или текущий баланс, если подписка передана до выставления первого счета). Остальная информация об использовании и выставленных счетах не передается с подпиской.

-   **Можно ли изменить предложение во время передачи?**

    Предложение должно оставаться неизменным. Чтобы изменить предложение, [обратитесь в службу поддержки](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Можно ли перенести подписку на учетную запись пользователя в другой стране?**

    Нет, в настоящее время эта услуга не поддерживается. Учетная запись пользователя-получателя должна находиться в той же стране.

-   **Может ли получатель использовать механизм изменения оплаты?**

    Да. Но в этой схеме существуют ограничения: теперь история выставления счетов в подписке разнесена на две учетные записи. Тем не менее преимущество заключается в том, что это можно сделать без [обращения в службу поддержки](http://go.microsoft.com/fwlink/?LinkID=619338).

## Дальнейшие действия после принятия владения подпиской

1. Теперь вы являетесь администратором учетной записи. Просмотрите и обновите данные об администраторе служб и соадминистраторах. Для управления администраторами перейдите в раздел "Параметры" на [классическом портале Azure](https://manage.windowsazure.com). [Подробнее](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Для управления подпиской и службами используйте управление доступом на основе ролей (RBAC). Посетите [портал Azure](https://portal.azure.com) [Дополнительные сведения о RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Обновите учетные данные, связанные со службами этой подписки. К ним относятся:
    -   Сертификаты управления, которые предоставляют пользователю права администратора для доступа к ресурсам подписки. Дополнительные сведения см. в статье [Создание и передача сертификата управления для Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	Ключи доступа для служб, таких как служба хранилища. Дополнительные сведения см. в статье [Просмотр, копирование и повторное создание ключей доступа к хранилищу](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	Учетные данные удаленного доступа для служб, таких как Виртуальные машины Azure
4. Обновите оповещения о выставлении счетов для этой подписки в [центре учетных записей Azure](https://account.windowsazure.com/Subscriptions) [Подробнее](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 	Если вы работаете с партнером, рекомендуется обновить ИД партнера для этой подписки. Это можно сделать в [центре учетных записей Azure](https://account.windowsazure.com/Subscriptions).

<!-----HONumber=AcomDC_1223_2015-->