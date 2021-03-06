<properties 
    pageTitle="Руководство. Интеграция Azure Active Directory с Jive | Microsoft Azure" 
    description="Узнайте, как использовать Jive вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
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

#Руководство. Интеграция Azure Active Directory с Jive

  
Цель данного руководства — показать интеграцию Azure и Jive. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Клиент в Jive
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Jive
2.  Настройка подготовки учетных записей пользователей

##Включение интеграции приложений для Jive
  
В этом разделе показано, как включить интеграцию приложений для Jive.

###Чтобы включить интеграцию приложений для Jive, выполните следующие действия.

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-jive-tutorial/IC700994.png "Приложения")

4.  Чтобы открыть **коллекцию приложений**, щелкните **Добавить приложение**, затем — **Добавить приложение для использования моей организацией**.

    ![Что необходимо сделать?](./media/active-directory-saas-jive-tutorial/IC700995.png "Что необходимо сделать?")

5.  В **поле поиска** введите **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  В области результатов выберите **Jive** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Настройка подготовки учетных записей пользователей
  
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для Jive. В рамках этой процедуры потребуется предоставить маркер безопасности пользователя, который необходимо запросить на веб-сайте Jive.com.
  
На следующем снимке экрана показан пример соответствующего диалогового окна в Azure AD.

![Настройка подготовки пользователей](./media/active-directory-saas-jive-tutorial/IC698794.png "Настройка подготовки пользователей")

###Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1.  На странице интеграции приложений **Jive** портала управления Azure нажмите **Настройка подготовки пользователей**, чтобы открыть диалоговое окно **Настройка подготовки пользователей**.

2.  На странице **Введите учетные данные Jive, чтобы включить автоматическую подготовку учетных записей пользователей** укажите следующие параметры конфигурации.

    1.  В текстовом поле **Имя пользователя администратора Jive** введите имя учетной записи Jive, которой в Jive.com назначен профиль **Системный администратор**.

    2.  В текстовом поле **Пароль администратора Jive** введите пароль для этой учетной записи.

    3.  В текстовом поле **URL-адрес клиента Jive** введите URL-адрес клиента Jive.

        >[AZURE.NOTE]URL-адрес клиента Jive — это URL-адрес, используемый вашей организацией для входа в Jive. Как правило, этот URL-адрес имеет следующий формат: **www.<организация>.jive.com**.

    4.  Нажмите **проверить** для проверки конфигурации.

    5.  Нажмите кнопку **Далее**, чтобы открыть страницу **Подтверждение**.

3.  На странице **Подтверждение** установите флаг, чтобы сохранить конфигурацию.
  
Теперь можно создать тестовую учетную запись, подождать 10 минут и проверить, синхронизирована ли учетная запись с Jive.com.

<!---HONumber=AcomDC_0121_2016-->