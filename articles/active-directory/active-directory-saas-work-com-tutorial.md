<properties 
    pageTitle="Руководство. Интеграция Azure Active Directory с Work.com | Microsoft Azure" 
    description="Узнайте, как использовать Work.com вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
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

#Руководство. Интеграция Azure Active Directory с Work.com
  
Цель данного руководства — продемонстрировать интеграцию Azure и Work.com.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка с поддержкой единого входа Work.com
  
По завершении работы с этим руководством пользователи AAD, которым будет назначен доступ к Work.com, смогут выполнять единый вход в приложение на веб-сайте Work.com вашей компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Work.com
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-work-com-tutorial/IC794105.png "Сценарий")

##Включение интеграции приложений для Work.com
  
В этом разделе показано, как включить интеграцию приложений для Work.com.

###Чтобы включить интеграцию приложений для Work.com, выполните следующие действия.

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-work-com-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-work-com-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-work-com-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Work.com**.

    ![Коллекция приложений](./media/active-directory-saas-work-com-tutorial/IC794106.png "Коллекция приложений")

7.  В области результатов выберите **Work.com** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Work.com со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат на сайт Work.com.

>[AZURE.NOTE]Чтобы настроить единый вход, также необходимо пользовательское имя домена Work.com. Вам требуется определить по крайней мере одно имя домена, а также проверить и развернуть его для всей организации.

###Чтобы настроить единый вход, выполните следующие действия.

1.  Войдите в клиент Work.com от имени администратора.

2.  Перейдите в раздел **Настройка**.

    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Настройка")

3.  В разделе **Администрирование** левой области навигации нажмите **Управление доменами**, чтобы развернуть соответствующий раздел, и нажмите **Мой домен**, чтобы открыть страницу **Мой домен**.

    ![Мой домен](./media/active-directory-saas-work-com-tutorial/IC767825.png "Мой домен")

4.  Чтобы проверить правильность настройки домена, убедитесь, что он находится в состоянии "**Шаг 4. Развернуто для пользователей**" и просмотрите "**Параметры моего домена**".

    ![Домен развернут для пользователя](./media/active-directory-saas-work-com-tutorial/IC784377.png "Домен развернут для пользователя")

5.  В другом окне браузера войдите на портал Azure.

6.  На странице интеграции с приложением **Work.com** нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794109.png "Настройка единого входа")

7.  На странице **Как пользователи должны входить в Work.com** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794110.png "Настройка единого входа")

8.  На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в Work.com** введите URL-адрес, используемый пользователями для входа в приложение Work.com (например,” *http://company.my.salesforce.com*”) и нажмите кнопку **Далее**:

    ![Настройка URL-адреса приложения](./media/active-directory-saas-work-com-tutorial/IC794111.png "Настройка URL-адреса приложения")

9.  На странице **Настройка единого входа в Work.com** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата локально на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794112.png "Настройка единого входа")

10. Войдите в клиент Work.com.

11. Перейдите в раздел **Настройка**.

    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Настройка")

12. Разверните меню **Управление безопасностью**, а затем щелкните **Параметры единого входа**.

    ![Параметры единого входа](./media/active-directory-saas-work-com-tutorial/IC794113.png "Параметры единого входа")

13. На странице диалогового окна **Параметры единого входа** сделайте следующее:

    ![SAML включен](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML включен")

    1.  Установите флажок **SAML включен**.
    2.  Нажмите кнопку **Создать**.

14. В разделе **Параметры единого входа SAML** сделайте следующее:

    ![Параметры единого входа SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "Параметры единого входа SAML")

    1.  В текстовом поле **Имя** введите имя конфигурации.  

        >[AZURE.NOTE]При вводе значения в поле **Имя** текстовое поле **Имя API** заполняется автоматически.

    2.  На странице диалогового окна **Настройка единого входа в Work.com** портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Издатель**.
    3.  Чтобы отправить скачанный сертификат, нажмите кнопку **Обзор**.
    4.  В текстовом поле **Идентификатор сущности** введите **https://salesforce-work.com**.
    5.  В поле **Тип удостоверения SAML** выберите значение **Проверочное утверждение содержит идентификатор федерации из объекта User**.
    6.  В поле **Расположение удостоверения SAML** выберите значение **Удостоверение находится в элементе NameIdentifier оператора Subject**.
    7.  На странице диалогового окна **Настройка единого входа в Work.com** портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес входа поставщика удостоверений**.
    8.  На странице диалогового окна **Настройка единого входа в Work.com** портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода поставщика удостоверений**.
    9.  В поле **Связывание запросов, инициированных поставщиком услуг** выберите значение **HTTP Post**.
    10. Щелкните **Сохранить**.

15. В левой области навигации портала Work.com щелкните **Управление доменами**, чтобы развернуть соответствующий раздел, и щелкните **Мой домен**, чтобы открыть страницу **Мой домен**.

    ![Мой домен](./media/active-directory-saas-work-com-tutorial/IC794115.png "Мой домен")

16. На странице **Мой домен** в разделе **Фирменная символика страницы входа** нажмите кнопку **Изменить**.

    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/IC767826.png "Фирменная символика страницы входа")

17. На странице **Фирменная символика страницы входа** в разделе **Служба проверки подлинности** отображается имя **Параметры единого входа SAML**. Выберите его, а затем нажмите кнопку **Сохранить**.

    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/IC784366.png "Фирменная символика страницы входа")

18. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794116.png "Настройка единого входа")

##Настройка подготовки учетных записей пользователей
  
Чтобы пользователи Azure Active Directory могли входить систему, их необходимо подготовить для Work.com.  
В случае с Work.com подготовка выполняется вручную.

###Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1.  Выполните вход на сайт компании Work.com в качестве администратора.

2.  Выберите пункт **Настройка**.

    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Настройка")

3.  Выберите **Управление пользователями > Пользователи**.

    ![Управление пользователями](./media/active-directory-saas-work-com-tutorial/IC784369.png "Управление пользователями")

4.  Щелкните **Новый пользователь**.

    ![Все пользователи](./media/active-directory-saas-work-com-tutorial/IC794117.png "Все пользователи")

5.  В разделе "Изменить пользователя" выполните следующие действия.

    ![Изменить пользователя](./media/active-directory-saas-work-com-tutorial/IC794118.png "Изменить пользователя")

    1.  Заполните текстовые поля **Фамилия**, **Псевдоним**, **Электронный адрес**, **Имя пользователя** и **Псевдоним** данными действующей учетной записи Azure Active Directory, которую нужно подготовить.
    2.  Выберите **Роль**, **Лицензия пользователя** и **Профиль**.
    3.  Щелкните **Сохранить**.  

        >[AZURE.NOTE]Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.

>[AZURE.NOTE]Вы можете использовать любые другие средства создания учетной записи пользователя Work.com или API, предоставляемые Work.com, для подготовки учетных записей пользователей AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которым нужно разрешить работу с приложением, назначив их.

###Чтобы назначить пользователей Work.com, выполните следующие действия.

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции с приложением Work.com нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-work-com-tutorial/IC794119.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-work-com-tutorial/IC767830.png "Да")
  
Подождите 10 минут и убедитесь, что учетная запись синхронизирована с Work.com.
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->