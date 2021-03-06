1. Откройте файл проекта mainpage.xaml.cs и добавьте следующий фрагмент кода в класс MainPage:
	
        private MobileServiceUser user;
        private async Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileServiceDotNetClient.LoginAsync(MobileServiceAuthenticationProvider.Twitter);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                await dialog.ShowAsync();
            }
        }

    Это создает переменную-член для хранения текущего пользователя и метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с использованием имени входа в Twitter.

    >[AZURE.NOTE]Если используется поставщик удостоверений, отличный от Twitter, измените значение <strong>MobileServiceAuthenticationProvider</strong> выше на значение для вашего поставщика.</p> </div>

2. Удалите или закомментируйте существующее переопределение метода **OnNavigatedTo** и замените его на следующий метод, который обрабатывает событие **Loaded** для страницы.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Этот метод вызывает новый метод **Authenticate**.

3. Замените конструктор MainPage следующим кодом:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Этот конструктор также регистрирует обработчик для события Loaded.
		
4. Нажмите клавишу F5 для запуска приложения и войдите в приложение с выбранным вами поставщиком удостоверений.

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

<!---HONumber=AcomDC_0211_2016-->