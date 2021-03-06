<properties
   pageTitle="Отладка приложения в Visual Studio | Microsoft Azure"
   description="Повысьте надежность и производительность служб, разрабатывая их в Visual Studio и локальном кластере разработки."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Отладка приложения Service Fabric с помощью Visual Studio

Вы можете сэкономить время и деньги, развернув приложение Azure Service Fabric и выполнив его отладку в кластере для разработки, состоящем из локальных компьютеров. С помощью Visual Studio можно развернуть приложение в локальном кластере и автоматически подключить отладчик ко всем экземплярам приложения.

1. Запустите кластер локальной разработки, выполнив действия, описанные в статье [Настройка среды разработки Service Fabric](service-fabric-get-started.md).

2. Нажмите клавишу **F5** или выберите в меню **Отладка** команду **Начать отладку**.

    ![Запуск отладки приложения][startdebugging]

3. Задайте в коде точки останова и поэтапно выполните приложение, выбирая соответствующие команды в меню **Отладка**.

    > [AZURE.NOTE] Visual Studio подключается ко всем экземплярам приложения. При пошаговом выполнении кода разные процессы могут одновременно достигать точек останова. Это приводит к возникновению параллельных сеансов. Чтобы этого не происходило, настройте отключение точек останова после их достижения, сделав их зависимыми от идентификатора потока, или используйте события диагностики.

4. Окно **События диагностики** открывается автоматически и позволяет просматривать данные о событиях диагностики в реальном времени.

    ![Просмотр событий диагностики в режиме реального времени][diagnosticevents]

5. Кроме того, вы можете открыть окно **События диагностики** вручную, используя обозреватель серверов. Разверните узел **Azure**, щелкните правой кнопкой мыши группу **Кластер Service Fabric** > **Просмотреть события диагностики**.

    ![Окно событий диагностики][viewdiagnosticevents]

6. События диагностики можно просматривать в автоматически созданном файле **ServiceEventSource.cs** и вызывать из кода приложения.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. В окне **События диагностики** поддерживаются фильтрация, приостановка и проверка событий в реальном времени. Фильтр представляет собой простой поиск по строкам сообщений о событиях, включая их содержимое.

    ![Фильтрация, приостановка, возобновление, а также проверка событий в реальном времени][diagnosticeventsactions]

8. Отладка служб аналогична отладке приложений. Точки останова для простой отладки, как правило, можно задать в Visual Studio. Несмотря на то что надежные коллекции реплицируются на нескольких узлах, они все равно реализуют интерфейс IEnumerable. Это означает, что вы можете использовать представление результатов в Visual Studio во время отладки для просмотра внутренних сведений. Просто задайте точку останова в любом месте своего кода.

    ![Запуск отладки приложения][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

- [Проверка службы Service Fabric](service-fabric-testability-overview.md)
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png

<!---HONumber=AcomDC_0204_2016-->