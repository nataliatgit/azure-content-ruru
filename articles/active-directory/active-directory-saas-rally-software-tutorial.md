<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с Rally Software | Microsoft Azure" 
    description="Узнайте, как использовать Rally Software с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и других задач." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Учебник. Интеграция Azure Active Directory с Rally Software
  
Цель данного учебника — показать интеграцию Azure и Rally Software. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Клиент Rally Software.
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Rally Software
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Сценарий")
##Включение интеграции приложений для Rally Software
  
В этом разделе показано, как включить интеграцию приложений для Rally Software.

###Чтобы включить интеграцию приложений для Rally Software, выполните следующие действия:

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **rally**.

    ![Коллекция приложений](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Коллекция приложений")

7.  В области результатов выберите **Rally Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить аутентификацию в Rally Software со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. В рамках этой процедуры потребуется отправить сертификат на сайт Rally Software.

###Чтобы настроить единый вход, выполните следующие действия:

1.  На странице интеграции с приложением **Rally Software** портала Azure AD нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Rally?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Единый вход Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Единый вход Microsoft Azure AD")

3.  На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес клиента Rally Software** введите свой URL-адрес, используя шаблон *https://\<имя-клиента>.rally.com*, а затем нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Настройка URL-адреса приложения")

4.  На странице **Настройка единого входа в Rally** нажмите кнопку "Загрузить метаданные", а затем сохраните эти данные локально на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Настройка единого входа")

5.  Выполните вход в клиент **Rally Software**.

6.  На панели инструментов вверху щелкните **Настройка**, затем выберите **Подписка**.

    ![Подписка](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Подписка")

7.  Нажмите кнопку **Действие** на панели инструментов вверху справа и выберите **Изменить подписку**.

8.  На диалоговой странице **Подписка** выполните указанные ниже действия, после чего нажмите кнопку **Сохранить и закрыть**.

    ![Аутентификация](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Аутентификация")

    1.  Из раскрывающегося списка «Аутентификация» выберите пункт **Аутентификация Rally или посредством единого входа**.
    2.  На диалоговой странице **Настройка единого входа в Rally Software** портала Azure скопируйте значение поля **Идентификатор поставщика удостоверений** и вставьте его в текстовое поле **URL-адрес поставщика удостоверений**.
    3.  На странице **Настройка единого входа в Rally Software** портала Azure скопируйте значение поля **URL-адрес удаленного выхода**.

9.  На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей
  
Чтобы пользователи AAD могли войти систему, их необходимо подготовить для приложения Rally Software, используя их имена пользователей Azure Active Directory.

###Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1.  Выполните вход в клиент Rally Software.

2.  Щелкните **Настройка > ПОЛЬЗОВАТЕЛИ**, затем **+ Добавить**.

    ![Пользователи](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Пользователи")

3.  Введите имя в текстовом поле "Новый пользователь" и щелкните **Добавить со сведениями**.

4.  В разделе **Создание пользователя** выполните следующие действия:

    ![Создание пользователя](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Создание пользователя")

    1.  В текстовом поле **Имя пользователя** введите имя пользователя Azure AD, которого хотите подготовить.
    2.  В текстовом поле **Адрес электронной почты** введите электронный адрес пользователя Azure AD, которого хотите подготовить.
    3.  Щелкните **Сохранить и закрыть**.

>[AZURE.NOTE]Вы можете использовать любые другие инструменты создания учетных записей пользователя Rally Software или API, предоставляемые Rally Software для подготовки учетных записей пользователя AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте доступ пользователям Azure AD, которые должны использовать приложение, назначив их.

###Чтобы назначить пользователей Rally Software, выполните следующие действия:

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции с приложением **Rally Software** нажмите **Назначить пользователей**.

    ![Назначение пользователей](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Да")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->