<properties
   pageTitle="Создание проекта Azure в Visual Studio | Microsoft Azure"
   description="Создание проекта Azure в Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tarcher" />

# Создание проекта Azure в Visual Studio

Средства Azure для Visual Studio предоставляют шаблон, который позволяет создавать облачные службы для Azure. Средства также помогают настраивать облачные службы, выполнять их отладку и развертывать их в Azure.

Решение Azure для облачных служб содержит следующие типы проектов:

- **Проект Azure**

    Проект Azure связан с проектами ролей в решении. Он включает определение службы и файлы конфигурации службы. Файл определения службы определяет параметры среды выполнения для приложения, в том числе требуемые роли, конечные точки и размер виртуальной машины. Файл конфигурации службы задает количество выполняемых экземпляров роли, а также значения параметров, определенных для роли. Дополнительные сведения об этих параметрах см. в разделе [Практическое руководство. Настройка ролей для облачной службы Azure с помощью Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Проект веб-роли**

    Рабочая роль выполняет фоновую обработку. Рабочая роль может взаимодействовать со службами хранилища и другими веб-службами. Рабочая роль может иметь любое количество конечных точек HTTP, HTTPS или TCP.

    - **Веб-роль ASP.NET** — для построения приложений ASP.NET с помощью веб-интерфейса
    - **Веб-роль ASP.NET MVC5**
    - **Веб-роль ASP.NET MVC4**
    - **Веб-роль ASP.NET MVC3**
    - **Веб-роль службы WCF** (для построения службы WCF)
    - **Веб-роль бизнес-приложений Silverlight** (требуется Visual Studio 2012)

- **Рабочая роль кэша**

    Роль, предоставляющая приложению выделенный кэш.

- **Рабочая роль с очередью служебной шины**

    Очередь служебной шины, предоставляющая функциональность очередей сообщений для взаимодействия с рабочим процессом. Дополнительные сведения см. в разделе [Использование очередей Service Bus](http://go.microsoft.com/fwlink/?LinkId=260560).

## Чтобы создать проект облачной службы в Visual Studio:

1. Запустите Microsoft Visual Studio от имени администратора.

1. В главном меню выберите **Файл**, **Создать**, **Проект**.

1. В области **Типы проектов** выберите **Облако** в узлах шаблонов проектов Visual C# или Visual Basic.

1. В области **Шаблоны** выберите **Облачная служба Azure**.

1. Укажите, какую версию платформы .NET Framework вы хотите использовать для разработки проекта.

1. Введите имя проекта, его расположение и имя решения. Нажмите кнопку **ОК**.

1. В диалоговом окне **Новый проект Azure** выберите роли, которые хотите добавить, и нажмите стрелку вправо, чтобы добавить их в решение. Вы можете добавить любое количество ролей.

1. Чтобы переименовать роль, добавленную в проект, выберите роль в диалоговом окне **Новый проект Azure** и щелкните значок **Переименовать** справа от нее. Вы также можете переименовать роль в решении после ее добавления.

<!---HONumber=AcomDC_0128_2016-->