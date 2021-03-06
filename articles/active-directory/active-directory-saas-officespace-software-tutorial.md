<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с OfficeSpace Software | Microsoft Azure" 
    description="Узнайте, как использовать OfficeSpace Software с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Учебник. Интеграция Azure Active Directory с OfficeSpace Software
  
Цель данного учебника — показать интеграцию Azure и OfficeSpace Software. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка OfficeSpace Software, настроенная для единого входа.
  
По завершении работы с этим руководством пользователи Azure AD, назначенные в OfficeSpace Software, будут иметь возможность единого входа в приложение на веб-сайте OfficeSpace Software компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для OfficeSpace Software
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Сценарий")
##Включение интеграции приложений для OfficeSpace Software
  
В этом разделе показано, как включить интеграцию приложений для OfficeSpace Software.

###Чтобы включить интеграцию приложений для OfficeSpace Software, выполните следующие действия:

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **OfficeSpace Software**.

    ![Коллекция приложений](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Коллекция приложений")

7.  В области результатов выберите **OfficeSpace Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить аутентификацию в OfficeSpace Software со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. Чтобы настроить единый вход для OfficeSpace Software, необходимо извлечь значение отпечатка из сертификата. Если вы не знакомы с этой процедурой, посмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

###Чтобы настроить единый вход, выполните следующие действия:

1.  На странице интеграции с приложением **OfficeSpace Software** портала Azure AD нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в OfficeSpace Software** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Настройка единого входа")

3.  На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в OfficeSpace Software** введите URL-адрес, используемый пользователями для входа в приложение OfficeSpace Software (например, **https://company.officespacesoftware.com*")), и нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Настройка URL-адреса приложения")

4.  На странице **Настройка единого входа в OfficeSpace Software** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата локально на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Настройка единого входа")

5.  В другом окне веб-браузера войдите на сайт OfficeSpace Software компании в качестве администратора.

6.  Выберите **Администратор > Соединители**.

    ![Администратор](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Администратор")

7.  Щелкните **Авторизация SAML**.

    ![Соединители](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Соединители")

8.  В разделе **Авторизация SAML** сделайте следующее:

    ![Настройка SAML](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Настройка SAML")

    1.  На странице **Настройка единого входа в OfficeSpace Software** портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес поставщика выхода**.
    2.  На странице **Настройка единого входа в OfficeSpace Software** портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Целевой URL-адрес IdP клиента**.
    3.  Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Отпечаток сертификата IdP клиента**.  

        >[AZURE.TIP]Дополнительные сведения можно узнать из видео [Как получить значение отпечатка сертификата](http://youtu.be/YKQF266SAxI).

    4.  Нажмите кнопку **Сохранить параметры**.

9.  На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей
  
Чтобы разрешить пользователям Azure AD вход в OfficeSpace Software, их необходимо подготовить для OfficeSpace Software. В случае OfficeSpace Software подготовка выполняется автоматически. С вашей стороны никакие действия не требуются. В случае необходимости пользователи создаются автоматически при первой попытке входа в систему.

>[AZURE.NOTE]Вы можете использовать любые другие инструменты создания учетных записей пользователя OfficeSpace Software или API, предоставляемые OfficeSpace Software для подготовки учетных записей пользователя AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей OfficeSpace Software, выполните следующие действия:

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции с приложением **OfficeSpace Software** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Да")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->