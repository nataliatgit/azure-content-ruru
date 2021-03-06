<properties 
    pageTitle="Руководство. Интеграция Azure Active Directory с Zoho Mail | Microsoft Azure" 
    description="Узнайте, как использовать Zoho Mail вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
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

#Руководство. Интеграция Azure Active Directory с Zoho Mail
  
Цель данного руководства — показать интеграцию Azure и Zoho Mail. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Клиент Zoho Mail
  
По завершении работы с этим руководством пользователи Azure AD, назначенные в Zoho Mail, смогут выполнять единый вход в приложение на веб-сайте Zoho Mail компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Zoho Mail
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Сценарий")

##Включение интеграции приложений для Zoho Mail
  
В этом разделе показано, как включить интеграцию приложений для Zoho Mail.

###Чтобы включить интеграцию приложений для Zoho Mail, выполните следующие действия.

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Zoho Mail**.

    ![Коллекция приложений](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Коллекция приложений")

7.  В области результатов выберите **Zoho Mail** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Zoho Mail со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

###Чтобы настроить единый вход, выполните следующие действия.

1.  На странице интеграции с приложением **Zoho Mail** портала Azure AD нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Zoho Mail** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Настройка единого входа")

3.  На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа в Zoho Mail** введите свой URL-адрес, используя следующий шаблон "**http://company.ZohoMail.com*", а затем нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Настройка URL-адреса приложения")

4.  На странице **Настройка единого входа в Zoho Mail** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Настройка единого входа")

5.  В другом окне браузера войдите на свой корпоративный сайт Zoho Mail в качестве администратора.

6.  Перейдите в раздел **Панель управления**.

    ![Панель управления](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Панель управления")

7.  Щелкните вкладку **Проверка подлинности SAML**.

    ![Проверка подлинности SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Проверка подлинности SAML")

8.  В разделе **Информация о проверке подлинности SAML** сделайте следующее:

    ![Сведения о проверке подлинности SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Сведения о проверке подлинности SAML")

    1.  На странице диалогового окна **Настройка единого входа в Zoho Mail** портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес входа**.
    2.  На странице диалогового окна **Настройка единого входа в Zoho Mail** портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
    3.  На странице диалогового окна **Настройка единого входа в Zoho Mail** портала Azure скопируйте значение поля **URL-адрес изменения пароля** и вставьте его в текстовое поле **URL-адрес изменения пароля**.
    4.  Создайте файл **в кодировке Base-64** из скачанного сертификата.  

        >[AZURE.TIP]Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    5.  Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Открытый ключ**.
    6.  В поле **Алгоритм** задайте значение **RSA**.
    7.  Нажмите кнопку **ОК**.

9.  На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Настройка единого входа")

##Настройка подготовки учетных записей пользователей
  
Чтобы пользователи Azure AD могли выполнить вход в Zoho Mail, они должны быть подготовлены для Zoho Mail. В случае с Zoho Mail подготовка выполняется вручную.

###Чтобы подготовить учетные записи пользователей, выполните следующие действия.

1.  Войдите на веб-сайт **Zoho Mail** компании в качестве администратора.

2.  Выберите **Панель управления > Почта и документы**.

3.  Щелкните **Сведения о пользователе > Добавить пользователя**.

    ![Добавить пользователя](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Добавить пользователя")

4.  В диалоговом окне **Добавление пользователей** сделайте следующее:

    ![Добавить пользователя](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Добавить пользователя")

    1.  Заполните текстовые поля **Имя**, **Фамилия**, **Электронный адрес** и **Пароль** данными действующей учетной записи Azure Active Directory, которую нужно подготовить.
    2.  Нажмите кнопку **ОК**.  

        >[AZURE.NOTE]Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.

>[AZURE.NOTE]Вы можете использовать любые другие средства создания учетной записи пользователя Zoho Mail или API, предоставляемые Zoho Mail, для подготовки учетных записей пользователей AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей Zoho Mail, выполните следующие действия.

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции с приложением **Zoho Mail** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Да")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->