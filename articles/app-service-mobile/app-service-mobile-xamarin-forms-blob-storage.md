<properties
    pageTitle="Подключение к службе хранилища Azure в приложении Xamarin.Forms"
    description="Добавление изображений в мобильное приложение списка задач Xamarin.Forms с использованием подключения к хранилищу BLOB-объектов Azure"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="02/03/2016"
    ms.author="donnam"/>

#Подключение к службе хранилища Azure в приложении Xamarin.Forms

## Обзор

Пакет SDK для клиента и сервера мобильных приложений Azure поддерживает автономную синхронизацию структурированных данных с данными конечной точки /tables, для чего используются операции CRUD. Обычно эти данные хранятся в базе данных или аналогичном хранилище, но в большинстве случаев такие хранилища не обеспечивают эффективное хранение двоичных данных больших объемов. Кроме того, связанные данные некоторых приложений хранятся в других местах (таких как хранилище BLOB-объектов и общие файловые ресурсы), поэтому возможность связывать записи в конечной точке /tables и других данных очень полезна.

В этом разделе показано, как обеспечить поддержку изображений для быстрого запуска списка задач в мобильных приложениях. Сначала необходимо выполнить указания в руководстве [Создание приложения Xamarin.Forms].

В этом руководстве вам предстоит создать учетную запись хранения и добавить строку подключения в серверную часть мобильного приложения. Затем нужно добавить новое наследование из нового типа мобильных приложений `StorageController<T>` в серверный проект.

>[AZURE.TIP] В этом руководстве содержится [сопровождающий пример](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/), который можно развернуть в учетной записи Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] или более поздняя версия.

* Компьютер Mac установленным ПО [Xcode](https://go.microsoft.com/fwLink/?LinkID=266532) 7.0 или более поздней версии и [Xamarin Studio](http://xamarin.com/download).

* Завершение работы с руководством [Создание приложения Xamarin.Forms]. В этой статье используется готовое приложение из него.

>[AZURE.NOTE] Если вы хотите приступить к работе со службой приложений Azure до регистрации и получения учетной записи Azure, перейдите на страницу [Пробное использование службы приложений](https://tryappservice.azure.com/?appServiceName=mobile). Там вы сможете немедленно создать кратковременное начальное мобильное приложение в службе приложений. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.

## Создайте учетную запись хранения.

1. Создайте учетную запись хранения, при этом следуйте руководству, приведенному в разделе [Создайте учетную запись хранения]. 

2. На портале Azure перейдите к только что созданной учетной записи хранения и щелкните значок **Ключи**. Скопируйте значение параметра **Основная строка подключения**.

3. Перейдите к серверной части мобильного приложения. Последовательно выберите **Все параметры** -> **Параметры приложения** -> **Строки подключения**, создайте ключ с именем `MS_AzureStorageAccountConnectionString` и используйте значение, скопированное в учетной записи хранения. В качестве типа ключа выберите **Настраиваемый**.

## Добавление контроллера хранилища в серверный проект

1. В Visual Studio откройте серверный проект .NET. Добавьте пакет NuGet [Microsoft.Azure.Mobile.Server.Files]. Выберите параметр **Включить предварительные выпуски**.

2. В Visual Studio откройте серверный проект .NET. Щелкните правой кнопкой мыши папку **Контроллеры**, а затем последовательно выберите **Добавить** -> **Контроллер** -> **Контроллер веб-API 2 — пустой**. Назовите контроллер `TodoItemStorageController`.

3. Добавьте операторы using:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Измените базовый класс на `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Добавьте в класс следующие методы:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Обновите конфигурацию веб-API, чтобы настроить маршрутизацию атрибутов. В файле **Startup.MobileApp.cs** добавьте в метод `ConfigureMobileApp()` после определения переменной `config` следующую строку:

        config.MapHttpAttributeRoutes();

7. Опубликуйте серверный проект в серверной части мобильного приложения.

###Маршруты, регистрируемые с помощью контроллера хранилища

Запись, управление которой обеспечивает новый контроллер `TodoItemStorageController`, позволяет использовать два вложенных ресурса:

- StorageToken:

    + HTTP POST обеспечивает создание маркера хранилища:
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles:

    + HTTP GET обеспечивает получение списка файлов, связанных с записью:
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE обеспечивает удаление файла по указанному идентификатору файлового ресурса:
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###Взаимодействие клиента и сервера

Обратите внимание, что у `TodoItemStorageController` *нет* маршрута для отправки или скачивания больших двоичных объектов. Причина в том, что при выполнении этих операций мобильный клиент взаимодействует с хранилищем BLOB-объектов *напрямую*, но для этого ему нужно сначала получить маркер SAS (подписанный URL-адрес) для безопасного доступа к определенному большему двоичному объекту или контейнеру. Эта архитектурная разработка очень важна, так как в ином случае масштаб и доступность серверной части мобильного приложения будут создавать ограничения доступа к хранилищу. Прямое подключение к службе хранилища Azure, напротив, предоставляет преимущества для мобильного клиента в виде таких функций, как автоматическое секционирование и географическое распределение.

Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Это означает, что клиенту можно предоставить ограниченное право на работу с объектами в вашей учетной записи хранения на определенный период времени и с определенным набором разрешений, не сообщая ему ключи доступа к своей учетной записи. Дополнительные сведения см. в статье [Подписи общего доступа. Часть 1: общие сведения о модели SAS].

На следующей схеме показано взаимодействие клиента и сервера. Перед отправкой файла клиент запрашивает маркер SAS у службы. Служба использует строку подключения к хранилищу, чтобы создать SAS, который затем возвращается клиенту. SAS ограничивает разрешения, позволяя получить доступ только к конкретному файлу или контейнеру и только на протяжении определенного промежутка времени. Затем мобильный клиент использует этот SAS и пакет SDK для клиента службы хранилища Azure, чтобы отправить файл в хранилище BLOB-объектов.

![Выполнение запроса на маркер SAS](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## Обновление клиентского приложения для поддержки изображений

Откройте проект быстрого запуска Xamarin.Forms в Visual Studio или Xamarin Studio.

>[AZURE.NOTE] Указания в этом руководстве касаются платформ Android, iOS и Магазина Windows и не применяются к устройствам Windows Phone.

###Добавление пакетов NuGet

Щелкните решение правой кнопкой мыши и выберите **Управление пакетами NuGet для решения**. Добавьте следующие пакеты NuGet во **все** проекты в решении. Выберите параметр **Включить предварительные выпуски**.

  - [Microsoft.Azure.Mobile.Client.Files;]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore;]

  - [PCLStorage.]

Для удобства в этом примере используется библиотека [PCLStorage], но это не обязательно при использовании пакета SDK для клиента мобильных приложений Azure.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/
[PCLStorage.]: https://www.nuget.org/packages/PCLStorage/

###Добавление интерфейса IPlatform

Создайте интерфейс `IPlatform` в основном проекте переносимой библиотеки. Затем необходимо добавить шаблон [DependencyService Xamarin.Forms ], который позволяет загрузить класс, соответствующий платформе, в среду выполнения. Позже вы добавите реализации, соответствующие используемой платформе, в каждый клиентский проект.

1. Добавьте операторы using:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Замените реализацию следующим кодом:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###Добавление класса FileHelper

1. Создайте класс `FileHelper` в основном проекте переносимой библиотеки. Добавьте операторы using:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Добавьте определение класса:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### Добавление обработчика синхронизации файлов

Создайте класс `TodoItemFileSyncHandler` в основном проекте переносимой библиотеки. Этот класс предусматривает обратные вызовы из пакета SDK для Azure, чтобы при добавлении или удалении файла соответствующая операция учитывалась в коде.

Пакет SDK для клиента мобильных приложений Azure не сохраняет данные файлов. Он вызывает реализацию `IFileSyncHandler`, который определяет, будут ли храниться файлы на локальном устройстве, и другие параметры хранения.

1. Добавьте операторы using:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Замените определение класса следующим кодом:

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###Обновление TodoItemManager

1. В **TodoItemManager.cs** раскомментируйте строку `#define OFFLINE_SYNC_ENABLED`.

2. В **TodoItemManager.cs** добавьте следующие операторы using:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. В конструкторе `TodoItemManager` добавьте следующий код после вызова метода `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. В конструкторе замените вызов `InitializeAsync` следующим кодом. Это обеспечит выполнение обратных вызовов при изменении записей в локальном хранилище. При использовании функции синхронизации файлов эти обратные вызовы служат для запуска обработчика синхронизации файлов.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. В `SyncAsync()` добавьте приведенный ниже код после вызова `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Добавьте следующие методы в `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###Добавление подробного представления

В этом разделе вы добавите подробное представление для элемента списка задач. Оно создается, когда пользователь выбирает элемент списка задач, и позволяет добавлять новые изображения в элемент.

1. Добавьте новый класс **TodoItemImage** в проект переносимой библиотеки со следующей реализацией:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Внесите изменения в файл **App.cs**. Замените инициализацию `MainPage` следующим кодом:
    
        MainPage = new NavigationPage(new TodoList());

3. В **App.cs** добавьте следующее свойство:

        public static object UIContext { get; set; }

4. Щелкните правой кнопкой мыши проект переносимой библиотеки и последовательно выберите **Добавить** -> **Новый элемент** -> **Кроссплатформенный** -> **Страница форм Xaml**. Присвойте представлению имя `TodoItemDetailsView`.

5. Откройте файл **TodoItemDetailsView.xaml** и замените текст ContentPage следующим:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Внесите изменения в файл **TodoItemDetailsView.xaml.cs** и добавьте следующие операторы using:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Замените реализацию `TodoItemDetailsView` следующим кодом:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###Обновление главного представления 

Обновите главное представление, чтобы при выборе элемента списка задач открывалось подробное представление.

В **TodoList.xaml.cs** замените реализацию `OnSelected` следующим кодом:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###Обновление проекта Android

Добавьте код, соответствующий платформе, в проект Android, в том числе код для скачивания файлов и использования камеры для создания нового изображения.

В этом коде используется [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) Xamarin.Forms, чтобы загрузить класс, соответствующий платформе, в среду выполнения.

1. Добавьте компонент **Xamarin.Mobile** в проект Android.

2. Добавьте новый класс `DroidPlatform` со следующей реализацией. Замените YourNamespace именем главного пространства имен проекта.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Внесите изменения в файл **MainActivity.cs**. В `OnCreate` добавьте приведенный ниже код до вызова `LoadApplication()`:

        App.UIContext = this;

###Обновление проекта iOS

Добавьте в проект iOS код соответствующей платформы.

1. Добавьте в проект iOS компонент **Xamarin.Mobile**.

2. Добавьте новый класс `TouchPlatform` со следующей реализацией. Замените YourNamespace именем главного пространства имен проекта.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Внесите изменения в файл **AppDelegate.cs** и раскомментируйте вызов `SQLitePCL.CurrentPlatform.Init()`.

###Обновление проекта Windows

1. Установите расширение Visual Studio [SQLite для Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Дополнительные сведения см. в руководстве [Включение автономной синхронизации для приложения для Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Внесите изменения в **Package.appxmanifest** и проверьте работу **веб-камеры**.

3. Добавьте новый класс `WindowsStorePlatform` со следующей реализацией. Замените YourNamespace именем главного пространства имен проекта.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##Сводка

В этой статье описано, как использовать поддержку новых файлов в пакете SDK для клиента и сервера мобильных приложений Azure в работе со службой хранилища Azure.

- Создайте учетную запись хранения и добавьте строку подключения в серверную часть мобильного приложения. Ключ к службе хранилища Azure есть только в серверной части. Мобильный клиент запрашивает маркер SAS при каждой попытке доступа к службе хранилища Azure. Дополнительные сведения о маркерах SAS в службе хранилища Azure см. в статье [Подписи общего доступа. Часть 1: общие сведения о модели SAS].

- Создайте контроллер с подклассами `StorageController`, чтобы обеспечить обработку запросов на маркеры SAS и получение файлов, связанных с записью. По умолчанию файлы связаны с записью по идентификатору записи, который используется в имени контейнера. Поведение можно настроить, указав реализацию `IContainerNameResolver`. Политику использования маркеров SAS также можно настроить.

- Пакет SDK для клиента мобильных приложений Azure не хранит данные файлов. Вместо этого он вызывает определенный `IFileSyncHandler`, который задает параметры хранения файлов на локальном устройстве (и определяет, будут ли они храниться). Следующий код предназначен для регистрации обработчика синхронизации:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + Когда для пакета SDK для клиента мобильных приложений Azure необходимо получить данные файла (например, в рамках процесса отправки), происходит вызов `IFileSyncHandler.GetDataSource`. Это позволяет управлять тем, как (и нужно ли) хранить файлы на локальном устройстве, и возвращать эти сведения, когда необходимо.

      + В рамках потока синхронизации файлов происходит вызов `IFileSyncHandler.ProcessFileSynchronizationAction`. Чтобы можно было определить, что должно происходить в приложении при возникновении такого события (например, автоматическое скачивание файла при его создании или обновлении либо удаление этого файла на локальном устройстве при его удалении на сервере), можно указать ссылку на файл и значение перечисления FileSynchronizationAction.

- `MobileServiceFile` можно использовать как при наличии подключения к сети, так и вне сети, применяя `IMobileServiceTable` или `IMobileServiceSyncTable` соответственно. Согласно автономному сценарию загрузка происходит, когда приложение вызывает `PushFileChangesAsync`. Это влечет обработку очереди автономных операций. Чтобы выполнить каждую операцию с файлом, пакет SDK для клиента мобильных приложений Azure вызывает метод `GetDataSource` в экземпляре `IFileSyncHandler` для извлечения содержимого файла, которое необходимо передать.

- Чтобы получить файлы элемента, нужно вызвать метод `GetFilesAsync` в экземпляре `IMobileServiceTable<T>` или IMobileServiceSyncTable<T>. Этот метод возвращает список файлов, связанных с элементом данных. (Примечание. Эта операция выполняется *локально*, и для возврата файлов используется состояние объекта во время последней синхронизации. Чтобы получить обновленный список файлов с сервера, сначала следует инициировать синхронизацию.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- Функция синхронизации файлов предусматривает использование уведомлений об изменении записей в локальном хранилище для получения записей, которые клиент получил при отправке push- и pull-уведомлений. Для этого необходимо включить уведомления на локальном устройстве и на сервере для контекста синхронизации, задав параметр `StoreTrackingOptions`.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Кроме того, доступны и другие возможности для отслеживания хранения, такие как локальные и серверные уведомления. Вы можете добавить настраиваемый обратный вызов, задав свойство `EventManager` `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Файлы можно добавлять в запись и удалять их из нее. Для этого нужно внести изменения прямо в хранилище BLOB-объектов, так как для связывания используется соглашение об именовании. Тем не менее, **при изменении связанных больших двоичных объектов всегда следует обновлять метку времени записи**. Пакет SDK для клиента мобильных приложений Azure всегда обновляет запись при добавлении или удалении файла.

    Это вызвано тем, что в локальном хранилище некоторых мобильных клиентов уже может быть такая запись. Если такой клиент выполнит добавочный pull-запрос, соответствующая запись не будет возвращена и клиент не запросит новые связанные файлы. Чтобы избежать этой проблемы, рекомендуется обновлять метку времени записи при любом изменении хранилища BLOB-объектов, в котором не задействован пакет SDK для клиента мобильных приложений Azure.

- В проекте клиента используется шаблон [DependencyService Xamarin.Forms], который позволяет загрузить класс, соответствующий платформе, в среду выполнения. В этом примере мы определили интерфейс `IPlatform` с реализациями для проектов для каждой из платформ.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Создание приложения Xamarin.Forms]: ../app-service-mobile-xamarin-forms-get-started.md
[DependencyService Xamarin.Forms]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[DependencyService Xamarin.Forms ]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files;]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore;]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Подписи общего доступа. Часть 1: общие сведения о модели SAS]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Создайте учетную запись хранения]: ../storage/storage-create-storage-account.md#create-a-storage-account

<!---HONumber=AcomDC_0211_2016-->