<properties
   pageTitle="Настройка проекта облачной службы в Visual Studio | Microsoft Azure"
   description="Узнайте, как настроить проект облачной службы Azure в Visual Studio в соответствии с требованиями к этому проекту."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/05/2016"
   ms.author="tarcher" />

# Настройка проекта облачной службы в Visual Studio

Проект облачной службы Azure можно настроить в соответствии с вашими требованиями к этому проекту. Для проекта можно настраивать свойства следующих категорий.

- **Публикация облачной службы в Azure**

  Свойство можно задать таким образом, чтобы не допустить случайного удаления облачной службы, уже развернутой в Azure.

- **Запуск или отладка облачной службы на локальном компьютере**

  Вы можете выбрать нужную конфигурацию службы и указать, должен ли запускаться эмулятор хранения Azure.

- **Проверка пакета облачной службы при его создании**

  Вы можете выбрать обработку всех предупреждений как ошибок, чтобы обеспечить развертывание пакета облачной службы без всяких ошибок. Это уменьшит время ожидания, если в процессе развертывания возникнет сбой.

На приведенной ниже иллюстрации показано, как выбрать нужную конфигурацию при локальном выполнении или отладке облачной службы. Как видно, в этом окне можно настроить любые необходимые свойства проекта.

![Настройка проекта Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Настройка проекта облачной службы Azure

1. Чтобы настроить проект облачной службы в **обозревателе решений**, откройте контекстное меню для проекта облачной службы и выберите **Свойства**.

  В редакторе Visual Studio откроется страница с именем проекта облачной службы.

1. Выберите вкладку **Разработка**.

1. Чтобы случайно не удалить уже существующее в Azure развертывание, в запросе перед удалением существующего развертывания выберите значение **True**.

1. Чтобы выбрать конфигурацию службы, которая будет использоваться при локальном запуске или отладке облачной службы, выберите **конфигурацию службы** в одноименном списке.

  >[AZURE.NOTE]Инструкции по созданию конфигурации службы см. в статье «Управление конфигурациями и профилями службы». Инструкции по настройке конфигурации службы для роли см. в статье [Настройка ролей для облачной службы Azure с помощью Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Чтобы при локальном запуске или отладке облачной службы запускался эмулятор хранилища Azure, в поле **Запуск эмулятора хранения Azure** выберите значение **True**.

1. Чтобы запретить публикацию при наличии ошибок проверки пакетов, в поле **Обрабатывать предупреждения как ошибки** выберите значение **True**.

1. Чтобы убедиться в том, что при каждом локальном запуске веб-роли в IIS Express используется один и тот же порт, в поле **Использовать порты веб-проектов** выберите значение **True**. Чтобы использовать определенный порт для определенного веб-проекта, откройте контекстное меню этого веб-проекта, перейдите на вкладку **Свойства**, а затем на вкладку **Веб** и измените номер порта в поле **URL-адрес проекта** в разделе **IIS Express**. Например, введите `http://localhost:14020` в качестве URL-адреса проекта.

1. Чтобы сохранить изменения, внесенные в свойства проекта облачной службы, нажмите кнопку **Сохранить** на панели инструментов.

## Дальнейшие действия

Дополнительные сведения о настройке проектов облачной службы Azure в Visual Studio см. в статье [Настройка проекта Azure с помощью нескольких конфигураций службы](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=AcomDC_0107_2016-->