<properties 
    pageTitle="Руководство. Интеграция Azure Active Directory с Benefitsolver | Microsoft Azure"
    description="Узнайте, как использовать Benefitsolver вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/02/2016" 
    ms.author="jeedes" />

#Руководство. Интеграция Azure Active Directory с Benefitsolver

Цель данного учебника — показать интеграцию Azure и Benefitsolver. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка с поддержкой единого входа Benefitsolver

После завершения этого руководства пользователи Azure AD, назначенные Benefitsolver, будут иметь возможность единого входа в приложение с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Benefitsolver
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Сценарий")
##Включение интеграции приложений для Benefitsolver

В этом разделе показано, как включить интеграцию приложений для Benefitsolver.

###Чтобы включить интеграцию приложений для Benefitsolver, выполните следующие действия.

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Benefitsolver**.

    ![Коллекция приложений](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Коллекция приложений")

7.  В области результатов выберите **Benefitsolver** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Benefitsolver со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. Приложение Benefitsolver ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML**. На следующем снимке экрана приведен пример.

![Атрибуты](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Атрибуты")

###Чтобы настроить единый вход, выполните следующие действия.

1.  На странице интеграции с приложением **Benefitsolver** портала Azure AD нажмите **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Benefitsolver?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Настройка единого входа")

3.  На странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка параметров приложения](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Настройка параметров приложения")

    1.  В текстовом поле **URL-адрес входа** введите **http://azure.benefitsolver.com**.
	2.  В текстовом поле **URL-адрес ответа** введите **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.


    3.  Нажмите кнопку **Далее**.

4.  На странице **Настройка единого входа в Benefitsolver** нажмите кнопку **Загрузить метаданные**, чтобы скачать их, а затем сохраните файл данных на локальный компьютер.

    ![Настройка единого входа](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Настройка единого входа")

5.  Отправьте загруженный файл метаданных в службу поддержки Benefitsolver.

    >[AZURE.NOTE] Настройка единого входа должна выполняться службой поддержки Benefitsolver. Как только единый вход для вашей подписки будет включен, вы получите уведомление.

6.  На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Настройка единого входа")

7.  В меню в верхней части экрана выберите пункт **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**.

    ![Атрибуты](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Атрибуты")

8.  Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.

    ![Атрибуты](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Атрибуты")

	|Имя атрибута|Значение атрибута|
    |---|---|
    |ClientID|Это значение необходимо получить у службы поддержки Benefitsolver.|
    |ClientKey|Это значение необходимо получить у службы поддержки Benefitsolver.|
    |LogoutURL|Это значение необходимо получить у службы поддержки Benefitsolver.|
    |EmployeeID|Это значение необходимо получить у службы поддержки Benefitsolver.|

    1.  Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
    2.  В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
    3.  В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
    4.  Нажмите **Завершено**.

9.  Нажмите кнопку **Применить изменения**.

##Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Benefitsolver, они должны быть подготовлены для Benefitsolver. При использовании Benefitsolver данные о сотрудниках находятся в приложении, которое заполняется с помощью файла переписи из вашей информационной системы персонала (обычно каждую ночь).

>[AZURE.NOTE] Вы можете использовать любые другие средства создания учетной записи пользователя Benefitsolver или API, предоставляемые Benefitsolver для подготовки учетных записей пользователя AAD.

##Назначение пользователей

Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей Benefitsolver, выполните следующие действия.

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции с приложением **Benefitsolver** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!----HONumber=AcomDC_0204_2016-->