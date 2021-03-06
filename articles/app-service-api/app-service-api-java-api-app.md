<properties
	pageTitle="Сборка и развертывание приложения API Java в службе приложений Azure"
	description="Узнайте, как создать пакет приложения API Java и развернуть его в службе приложений Azure."
	services="app-service\api"
	documentationCenter="java"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="02/22/2016"
	ms.author="bradygaster"/>

# Сборка и развертывание приложения API Java в службе приложений Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

В этом учебнике показано, как создать приложение Java и развернуть его в приложения API службы приложений Azure, используя [Git](http://git-scm.com). Указания, приведенные в этом учебнике, применимы в любой операционной системе, на которой может работать Java. Код в этом руководстве создан с помощью [Maven](https://maven.apache.org/). [Jax-RS](https://jax-rs-spec.java.net/) используется для создания службы RESTful и формируется на основе спецификации метаданных [Swagger](http://swagger.io) при помощи [редактора Swagger](http://editor.swagger.io/).

## Предварительные требования

1. [Java Developer Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (или более поздней версии).
1. Платная или [бесплатная пробная версия](https://azure.microsoft.com/pricing/free-trial/) подписки на [Microsoft Azure](https://azure.microsoft.com/).
1. Установка [Maven](https://maven.apache.org/) на компьютере, на котором ведется разработка.
1. Установка Git на компьютере, на котором ведется разработка. 

## Формирование API на основе скаффолдинга при помощи Swagger.io
В онлайн-редакторе Swagger.io можно ввести код Swagger в формате JSON или YAML, представляющий структуру API. После завершения разработки контактной зоны API можно экспортировать код в различные платформы и структуры. В следующем разделе код, сформированный на основе скаффолдинга, будет изменен для добавления возможности использования макетов.

Мы начнем со вставки текста JSON-кода Swagger в редактор swagger.io, который затем будет использован для создания кода с применением JAX-RS для доступа к конечной точке REST API. Затем вы измените код, созданный на основе скаффолдинга, для возврата данных макетов, при этом REST API будет смоделирован поверх механизма обеспечения постоянного хранения данных.

1. Скопируйте приведенный ниже JSON-код Swagger в буфер обмена.

		{
			"swagger": "2.0",
			"info": {
				"version": "v1",
				"title": "Contact List",
				"description": "A Contact list API based on Swagger and built using Java"
			},
			"host": "localhost",
			"schemes": [
				"http",
				"https"
			],
			"basePath": "/api",
			"paths": {
				"/contacts": {
					"get": {
						"tags": [
							"Contact"
						],
						"operationId": "contacts_get",
						"consumes": [],
						"produces": [
							"application/json",
							"text/json"
						],
						"responses": {
							"200": {
								"description": "OK",
								"schema": {
									"type": "array",
									"items": {
										"$ref": "#/definitions/Contact"
									}
								}
							}
						},
						"deprecated": false
					}
				},
				"/contacts/{id}": {
					"get": {
						"tags": [
							"Contact"
						],
						"operationId": "contacts_getById",
						"consumes": [],
						"produces": [
							"application/json",
							"text/json"
						],
						"parameters": [
							{
								"name": "id",
								"in": "path",
								"required": true,
								"type": "integer",
								"format": "int32"
							}
						],
						"responses": {
							"200": {
								"description": "OK",
								"schema": {
									"type": "array",
									"items": {
										"$ref": "#/definitions/Contact"
									}
								}
							}
						},
						"deprecated": false
					}
				}
			},
			"definitions": {
				"Contact": {
					"type": "object",
					"properties": {
						"Id": {
							"format": "int32",
							"type": "integer"
						},
						"Name": {
							"type": "string"
						},
						"EmailAddress": {
							"type": "string"
						}
					}
				}
			}
		}
		
1. Перейдите в [онлайн-редактор Swagger](http://editor.swagger.io/). В редакторе щелкните в меню **Файл -> Вставить JSON**.

    ![Вставить JSON](media/app-service-api-java-api-app/paste-json.png)

1. Вставьте JSON-код Swagger API списка контактов, который был скопирован ранее.

    ![Вставленный Swagger](media/app-service-api-java-api-app/pasted-swagger.png)

1. Просмотрите страницы документации и сводные данные API, отображаемые в редакторе.

    ![Просмотр документов, созданных при помощи Swagger](media/app-service-api-java-api-app/view-swagger-generated-docs.png)

1. В меню выберите **Создать сервер -> JAX RS**, чтобы создать серверный код на основе скаффолдинга, который позже будет изменен для добавления возможности реализации макетов.

    ![Пункт меню «Создать код»](media/app-service-api-java-api-app/generate-code-menu-item.png)
	
	После создания кода появится ZIP-файл для скачивания. Этот файл содержит код, сформированный генератором кода Swagger на основе скаффолдинга, а также все связанные сценарии сборки. Распакуйте всю библиотеку в каталог на рабочей станции, где ведется разработка.

## Измените код для добавления возможности реализации API.
В этом разделе вы замените серверную реализацию созданного кода на пользовательский код. Новый код вернет ArrayList сущностей Contact вызывающему клиенту.

1. Откройте файл модели *Contact.java*, расположенный в папке *src/gen/java/io/swagger/model*, с помощью [Visual Studio Code](https://code.visualstudio.com) или текстового редактора на ваш выбор. 

    ![Открыть файл модели контактов](media/app-service-api-java-api-app/open-contact-model-file.png)

1. Добавьте следующий конструктор в класс **Contact**.

        public Contact(Integer id, String name, String email) 
		{
			this.id = id;
			this.name = name;
			this.emailAddress = email;
		}

1. Откройте файл реализации службы *ContactsApiServiceImpl.java*, расположенный в папке *src/main/java/io/swagger/api/impl*, с помощью [Visual Studio Code](https://code.visualstudio.com) или текстового редактора на ваш выбор.

    ![Открыть файл кода службы контактов](media/app-service-api-java-api-app/open-contact-service-code-file.png)

1. Перезапишите код в файле на новый, чтобы добавить возможность реализации макетов в код службы.

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                    {
                        ret = list.get(i);
                    }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Откройте окно командной строки.

1. Выполните следующую команду Maven для создания кода и его локального запуска при помощи сервера приложений Jetty.

		mvn package jetty:run
		
1. В командном окне должно быть видно, что Jetty запустил код на порте 8080.

	![Открыть файл кода службы контактов](media/app-service-api-java-api-app/run-jetty-war.png)
	
1. Используйте [Postman](https://www.getpostman.com/) для запроса метода API, позволяющего получить все контакты, который расположен по адресу http://localhost:8080/api/contacts.

	![Вызвать API контактов](media/app-service-api-java-api-app/calling-contacts-api.png)
	
1. Используйте [Postman](https://www.getpostman.com/) для запроса метода API, позволяющего получить определенный контакт, который расположен по адресу http://localhost:8080/api/contacts/2.

	![Вызвать API контактов](media/app-service-api-java-api-app/calling-specific-contact-api.png)
	
1. Наконец, создайте WAR-файл Java (веб-архив), выполнив следующую команду Maven в консоли.

		mvn package war:war
		
	После создания WAR-файл будет помещен в папку **target**. Перейдите в папку **target** и переименуйте WAR-файл **ROOT.war** (убедитесь, что регистр букв соответствует этому формату).
	
		rename swagger-jaxrs-server-1.0.0.war ROOT.war
		
	Наконец, выполните следующие команды для создания папки **deploy**, предназначенной для развертывания WAR-файла в Azure.
	
		mkdir deploy
		mkdir deploy\webapps
		copy target\ROOT.war deploy\webapps
		cd deploy
	
## Публикация выходных данных в службе приложений Azure
В этом разделе вы узнаете, как создать приложение API при помощи портала Azure, подготовить это приложение API для размещения приложений Java и развернуть новый WAR-файл в службе приложений Azure, чтобы запустить новое приложение API.

1. Создайте приложение API на [портале Azure](https://portal.azure.com/), выбрав в меню **Создать -> Интернет + мобильные устройства -> Приложение API**.
	
	![Создать приложение API](media/app-service-api-java-api-app/create-api-app.png)

1. В колонке настроек приложения API щелкните **Параметры приложения**. Затем выберите последние версии Java в меню версий Java и последнюю версию Tomcat в меню веб-контейнера.

	![Настройка Java в колонке приложения API](media/app-service-api-java-api-app/set-up-java.png)

1. Щелкните пункт **Учетные данные развертывания** и укажите имя пользователя и пароль, которые вы хотите использовать для публикации файлов в приложении API.

	![Сброс учетных данных развертывания](media/app-service-api-java-api-app/deployment-credentials.png)

1. Щелкните пункт меню параметров **Непрерывное развертывание**. Затем нажмите кнопку **Выбрать источник** и выберите вариант **Локальный репозиторий Git**. В результате будет создан работающий в Azure репозиторий Git, который связан с приложением API. Каждый раз при фиксации кода в *главной* ветви репозитория Git код будет публиковаться в запущенном в данный момент экземпляре приложения API.

	![Настройка локального репозитория Git](media/app-service-api-java-api-app/select-git-repo.png)

1. Скопируйте URL-адрес нового репозитория Git в буфер обмена. Сохраните его, потому что он понадобится чуть позже.

	![Настройка нового репозитория Git для приложения](media/app-service-api-java-api-app/copy-git-repo-url.png)

1. Отправьте WAR-файл в онлайн-репозиторий при помощи Git. Для этого перейдите в созданную ранее папку **deploy**, из которой можно с легкостью зафиксировать код в репозитории, работающем в службе приложений. Открыв окно консоли и перейдя в папку, в которой находится папка с веб-приложениями, запустите следующие команды Git, чтобы запустить процесс и начать развертывание.

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [YOUR GIT URL]		
		git push azure master
		
	После отправки запроса на **передачу** потребуется ввести пароль, созданный ранее для учетных данных развертывания. При вводе пароля на портале станет видно, что обновление получено и развернуто.
		
1. При повторном использовании Postman для выполнения недавно развернутого приложения API, работающего в службе приложений Azure, станет видно, что поведение приложения согласовано и что теперь оно возвращает данные контактов должным образом, а также использует изменения простого кода по отношению к сформированному на основе скаффолдинга Java-коду Swagger.io.

	![Использование REST API контактов Java в Azure в реальном времени](media/app-service-api-java-api-app/postman-calling-azure-contacts.png)
	
## Дальнейшие действия
Следуя инструкциям в этой статье, вы смогли выполнить требуемые задачи с JSON-файлом Swagger и определенным Java-кодом, сформированным на основе скаффолдинга и полученным при помощи редактора Swagger.io. В результате внесения простых изменений и развертывания Git вы получили рабочее приложение API, написанное на Java. В следующем руководстве из серии «Приступая к работе с приложениями API» показано, как [использовать приложения API в клиентах JavaScript с помощью CORS](app-service-api-cors-consume-javascript.md).

Далее рекомендуем вам ознакомиться с дополнительными сведениями о [пакете SDK для Java для службы хранилища](../storage/storage-java-how-to-use-blob-storage.md). С помощью этого пакета вы сможете сохранять большие двоичные объекты JSON. Кроме того, вы можете использовать [пакет SDK для Java для Document DB](../documentdb/documentdb-java-application.md). Он позволяет сохранять данные контактов в Azure Document DB.

Дополнительную информацию об использовании Java в Azure см. в разделе [Центр разработчиков Java](/develop/java/).

<!---HONumber=AcomDC_0302_2016-->