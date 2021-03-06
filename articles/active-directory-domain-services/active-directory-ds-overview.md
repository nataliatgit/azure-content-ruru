<properties
	pageTitle="Обзор предварительной версии служб доменов Azure Active Directory | Microsoft Azure"
	description="Обзор служб доменов Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Службы доменов Azure AD *(предварительная версия)*

## Обзор
Службы инфраструктуры Azure позволяют выполнять гибкое развертывание разнообразных вычислительных решений. С помощью виртуальных машин Azure вы можете очень быстро развертывать нужные вам объекты с применением поминутной тарификации. Благодаря поддержке Windows, Linux, SQL Server, Oracle, IBM, SAP и BizTalk вы можете развертывать любые рабочие нагрузки и любые языковые пакеты практически в любой операционной системе. Эти преимущества позволяют переносить в Azure устаревшие приложения, развернутые локально, с целью сокращения эксплуатационных расходов.

Главным преимуществом миграции локальных приложений в Azure является поддержка идентификации, требуемой такими приложениями. Приложения с поддержкой каталогов могут использовать доступ LDAP для чтения или записи к корпоративному каталогу, а также функцию встроенной проверки подлинности Windows (методом Kerberos или NTLM) для проверки подлинности конечных пользователей. Бизнес-приложения, выполняемые в среде Windows Server, обычно развертываются на машинах, присоединенных к домену. Это обеспечивает безопасное управление ими с использованием групповой политики. Для быстрого перемещения локальных приложений в облако подобная зависимость от корпоративной инфраструктуры идентификации должна быть устранена.

Чтобы обеспечить поддержку идентификации в приложениях, развернутых в Azure, администраторы часто обращаются к следующим решениям.

- Развертывание VPN-подключения типа «сеть — сеть» между выполняемыми в службах инфраструктуры Azure рабочими нагрузками и локальным корпоративным каталогом.
- Расширение корпоративной инфраструктуры домена или леса AD путем настройки реплик контроллеров домена с помощью виртуальных машин Azure.
- Развертывание отдельного домена в Azure с помощью контроллеров домена, развертываемых в качестве виртуальных машин Azure.

Все эти подходы отличаются дороговизной и значительными затратами на администрирование. Администраторам необходимо развертывать контроллеры домена с использованием виртуальных машин в Azure. Впоследствии им нужно управлять этими виртуальными машинами, обеспечивать их безопасность, выполнять резервное копирование, а также исправлять, отслеживать и устранять неполадки. Зависимость от VPN-подключений к локальному каталогу повышает уязвимость развернутых в Azure рабочих нагрузок при временных сбоях или простоях в сети. Это, в свою очередь, негативно влияет на работоспособность и надежность таких приложений.

Службы доменов Azure AD являются более простой альтернативой упомянутым подходам.


## Введение в службы доменов Azure AD
Службы доменов Azure AD предоставляют управляемые службы доменов (отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, выполнение проверки подлинности методом Kerberos или NTLM и т. д.), которые полностью совместимы с Windows Server Active Directory. Службы доменов Azure AD позволяют использовать такие службы без необходимости развертывать, управлять и исправлять контроллеры домена в облаке. Службы доменов Azure AD интегрируются с существующим клиентом Azure AD, вследствие чего пользователи могут входить в систему с использованием корпоративных учетных данных. Кроме того, можно использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам, обеспечивая таким образом более плавное перемещение локальных ресурсов в службы инфраструктуры Azure.

Службы доменов Azure AD работают эффективно независимо от спецификаций клиента Azure AD (облачный клиент или синхронизированный с локальной службой Active Directory).

### Службы доменов Azure AD для облачных организаций
Облачный клиент Azure AD (часто называемый управляемым клиентом) не имеет никаких локальных идентификационных данных. Иными словами, данные о пользователях, их паролях и членстве в группах располагаются в облаке (т. е. создаются и управляются в Azure AD). Рассмотрим в качестве примера компанию Contoso, которая является облачным клиентом Azure AD. Как показано на рисунке ниже, администратор Contoso настроил виртуальную сеть в службах инфраструктуры Azure. Приложения и серверные рабочие нагрузки развертываются в этой виртуальной сети на виртуальных машинах Azure. Поскольку Contoso является облачным клиентом, все удостоверения пользователей, а также их учетные данные и сведения о членстве в группах создаются и управляются в Azure AD.

![Обзор служб доменов Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

ИТ-администратор Contoso может включить службы доменов Azure AD для своего клиента Azure AD, сделав их доступными в этой виртуальной сети. После такой настройки службы доменов Azure AD подготавливают управляемый домен, делая его доступным в виртуальной сети. Все учетные записи и учетные данные пользователей, а также сведения о членстве в группах, доступные в клиенте Azure AD Contoso, также будут доступны в созданном домене. Эта функция позволяет пользователям входить в домен с использованием корпоративных учетных данных, например при удаленном подключении к компьютерам, присоединенным к домену через удаленный рабочий стол. Администраторы могут предоставлять доступ к ресурсам в домене с использованием членства в существующих группах. В приложениях, развернутых на виртуальных машинах в виртуальной сети, доступны такие преимущества служб доменов, как возможность присоединения к домену, чтения LDAP, привязки LDAP, проверки подлинности NTLM и Kerberos, использования групповой политики и т. д.

Ниже приведены некоторые ключевые характеристики управляемого домена, который подготавливается службами доменов Azure AD.

- ИТ-администратору Contoso не требуется управлять, исправлять и отслеживать этот домен или какие-либо контроллеры этого управляемого домена.
- Нет необходимости управлять репликацией AD для этого домена. Учетные записи и учетные данные пользователей, а также сведения о членстве в группах, доступные в клиенте Azure AD Contoso, автоматически становятся доступными и в этом управляемом домене.
- Поскольку домен управляется службами доменов Azure AD, ИТ-администратор Contoso не имеет прав администратора домена или администратора предприятия в этом домене.


### Службы доменов Azure AD для гибридных организаций
Организации с гибридной ИТ-инфраструктурой используют сочетание облачных и локальных ресурсов. Такие организации синхронизируют сведения об удостоверении, содержащиеся в локальном каталоге, с клиентом Azure AD. Так как гибридные организации, как правило, переносят большинство локальных приложений в облако (особенно это касается устаревших приложений с поддержкой каталогов), службы доменов Azure AD могут быть им очень полезны.

Корпорация Litware развернула [Azure AD Connect](../active-directory/active-directory-aadconnect.md), чтобы синхронизировать сведения об удостоверениях, расположенные в локальном каталоге, в клиент Azure AD. Эти данные представлены учетными записями пользователей, хэшами учетных данных для проверки подлинности (синхронизация паролей) и сведениями о членстве в группах. Обратите внимание, что **синхронизация паролей является обязательной для гибридных организаций при использовании служб доменов Azure AD**. Это связано с тем, что в управляемом домене, предоставляемом службами доменов Azure AD, учетные данные пользователей требуются для проверки подлинности пользователей методами NTLM или Kerberos.

![Службы доменов Azure AD для корпорации Litware](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

На рисунке выше показано, как организации с гибридной ИТ-инфраструктурой (как корпорация Litware), могут использовать службы доменов Azure AD. Приложения и серверные рабочие нагрузки Litware, которым требуются службы доменов, развертываются в виртуальной сети в службах инфраструктуры Azure. ИТ-администратор Litware может включить службы доменов Azure AD в своем клиенте Azure AD, сделав управляемый домен доступным в этой виртуальной сети. Поскольку Litware — это организация с гибридной ИТ-инфраструктурой, расположенные в локальном каталоге организации учетные записи и учетные данные пользователей, а также сведения о членстве в группах синхронизируются в клиент Azure AD. Эта функция позволяет пользователям входить в домен с использованием корпоративных учетных данных, например при удаленном подключении к компьютерам, присоединенным к домену через удаленный рабочий стол. Администраторы могут предоставлять доступ к ресурсам в домене с использованием членства в существующих группах. В приложениях, развернутых на виртуальных машинах в виртуальной сети, доступны такие преимущества служб доменов, как возможность присоединения к домену, чтения LDAP, привязки LDAP, проверки подлинности NTLM и Kerberos, использования групповой политики и т. д.

Ниже приведены некоторые ключевые характеристики управляемого домена, который подготавливается службами доменов Azure AD.

- Это отдельный управляемый домен. Он не является расширением локального домена Litware.
- ИТ-администратору Litware не требуется управлять, исправлять и отслеживать этот домен или какие-либо контроллеры этого управляемого домена.
- Нет необходимости управлять репликацией AD для этого домена. Расположенные в локальном каталоге Litware учетные записи и учетные данные пользователей, а также сведения о членстве в группах синхронизируются в Azure AD через Azure AD Connect. Перенесенные данные автоматически становятся доступными в этом управляемом домене.
- Поскольку домен управляется службами доменов Azure AD, ИТ-администратор Litware не имеет прав администратора домена или администратора предприятия в этом домене.


## Преимущества
Использование служб доменов Azure AD отличается следующими преимуществами.

-	**Простота** — вы можете быстро и легко обеспечить поддержку идентификации, требуемой в виртуальных машинах, развернутых в службах инфраструктуры Azure. При этом вам не нужно ни развертывать инфраструктуру удостоверений в Azure с последующим управлением ею, ни настраивать обратную связь с локальной инфраструктурой удостоверений.

-	**Интеграция** — службы доменов Azure AD тесно интегрированы с клиентом Azure AD. Теперь вы можете использовать Azure AD как интегрированный облачный корпоративный каталог, учитывающий потребности современных приложений и традиционных приложений с поддержкой каталога.

-	**Совместимость** — так как службы доменов Azure AD основаны на проверенной инфраструктуре Windows Server Active Directory корпоративного класса, для приложений теперь предусмотрена большая степень совместимости с функциями Windows Server Active Directory. Обратите внимание, что не все функции, доступные в среде Windows Server AD, в настоящее время доступны в службах доменов Azure AD. Однако доступные функции совместимы с соответствующими функциями Windows Server AD, которые вы используете в своей локальной инфраструктуре. Включение в службу доменов Azure AD таких решений, как LDAP, Kerberos, NTLM, использование групповой политики и присоединение к домену, — это продуманное предложение, протестированное и оптимизированное на различных версиях Windows Server.

-	**Экономия** — используя службы доменов Azure AD, вы можете минимизировать расходы на инфраструктуру и управление инфраструктурой удостоверений для поддержки традиционных приложений с поддержкой каталогов. Такие приложения можно переместить в службы инфраструктуры Azure, что позволит существенно сократить эксплуатационные расходы.

<!---HONumber=AcomDC_0128_2016-->