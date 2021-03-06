<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Lesson.ly | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Lesson.ly."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с Lesson.ly

Цель этого руководства — показать, как интегрировать Lesson.ly с Azure Active Directory (Azure AD).<br>Интеграция Lesson.ly с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Lesson.ly.
- Вы можете включить автоматический вход пользователей в Lesson.ly (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure Active Directory

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Lesson.ly, вам потребуется:

- подписка Azure AD;
- подписка Lesson.ly с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Lesson.ly из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Lesson.ly из коллекции
Чтобы настроить интеграцию Lesson.ly с Azure AD, необходимо добавить Lesson.ly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Lesson.ly из коллекции, выполните следующие действия.**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Lesson.ly**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)<br>
7. В области результатов выберите **Lesson.ly** и щелкните **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)<br>
##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Lesson.ly с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Lesson.ly соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Lesson.ly.<br> Чтобы установить эту связь, следует назначить значение **имени пользователя** в Azure AD в качестве значения **имени пользователя** в Lesson.ly.

Чтобы настроить и проверить единый вход Azure AD в Lesson.ly, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Lesson.ly](#creating-a-Lessonly-test-user)** требуется для создания в Lesson.ly пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Lesson.ly со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

Приложение Lesson.ly предусматривает использование проверочных утверждений SAML в определенном формате, что предполагает добавление настраиваемых сопоставлений атрибутов в конфигурацию **атрибутов токена SAML**. На следующем снимке экрана приведен пример. <br><br> ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) <br>

**Чтобы настроить единый вход Azure AD в Lesson.ly, выполните следующие действия.**

1. На странице интеграции с приложением Lesson.ly портала Azure AD в меню в верхней части страницы щелкните **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**. <br><br> ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) <br>

2. Чтобы добавить обязательные сопоставления атрибутов, сделайте следующее: <br><br> ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) <br> а) для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**;<br> б) в текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.<br>; в) в списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки;<br> г) Щелкните **Завершить**.

3. Нажмите кнопку **Применить изменения**.

4. В браузере нажмите кнопку **Назад**, чтобы снова открыть диалоговое окно "Быстрый запуск".

5. Нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

6. На странице **Как пользователи должны входить в Lesson.ly?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) <br>

7. На диалоговой странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) <br>


    а. В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый пользователями для входа в приложение Lesson.ly, в таком формате: **hhttps://companyname.Lesson.ly/signin**. Значение **companyname** в ссылке необходимо заменить фактическим именем.


8. На странице **Настройка единого входа в Lesson.ly** сделайте следующее: <br><br>![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


9. Чтобы получить помощь по настройке единого входа для приложения, обратитесь в службу поддержки Lesson.ly, используя адрес dev@lesson.ly. Прикрепите к сообщению скачанный сертификат и укажите URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода), чтобы специалисты Lesson.ly смогли настроить единый вход со своей стороны.


10. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) <br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) <br>

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) <br>

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. В диалоговом окне на странице **Получение временного пароля** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) <br>

8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) <br>

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Lesson.ly

Цель этого раздела — создать пользователя с именем Britta Simon в Lesson.ly. Приложение Lesson.ly поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Lesson.ly (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Lesson.ly.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Lesson.ly. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon в Lesson.ly, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **Lesson.ly**. <br><br>![Настройка единого входа](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) <br>

1. В верхнем меню щелкните **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент Lesson.ly на панели доступа, вы автоматически войдете в приложение Lesson.ly.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0204_2016-->