<properties 
   pageTitle="Заметки о выпуске, посвященные обновлению 2 для устройств серии StorSimple 8000 | Microsoft Azure"
   description="В статье описываются новые возможности, проблемы и способы их решения для обновления 2, предназначенного для устройств серии StorSimple 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="02/12/2016"
   ms.author="v-sharos" />

# Заметки о выпуске, посвященные обновлению 2 для устройств серии StorSimple 8000  

## Обзор

В приведенных ниже заметках о выпуске описаны новые функции и критические открытые проблемы в обновлении 2 для устройств серии StorSimple 8000. Здесь также содержится список обновлений программного обеспечения, драйверов и встроенного ПО дисков StorSimple для этого выпуска.

Обновление 2 можно применить к любому устройству StorSimple под управлением программного обеспечения версии (GA) с обновлениями версий 0.1-1.2. Версия устройства, связанная с обновлением 2 — 6.3.9600.17673.

Перед развертыванием обновления в решении StorSimple, просмотрите сведения, содержащиеся в заметках о выпуске.

>[AZURE.IMPORTANT]
> 
- Установка обновления (включая обновления Windows) занимает примерно 4-7 часов. 
- Обновление 2 содержит обновления программного обеспечения, драйвера LSI и встроенного ПО SSD.
- Для новых выпусков обновления могут отображаться не сразу, поскольку развертывание обновлений выполняется поэтапно. Подождите несколько дней и затем повторите попытку поиска обновлений, так как они станут доступными в ближайшее время.


## Новые возможности в обновлении 2

Обновление 2 содержит следующие новые функции.

- **Локально закрепленные тома** — в предыдущих выпусках серии StorSimple 8000 блоки данных передавались в облако на основе использования. Гарантировать, что эти блоки продолжат размещаться локально, было невозможно. В обновлении 2 при создании тома можно назначить том как локально закрепленный, и основные данные с этого тома не будут передаваться в облако. Моментальные снимки локально закрепленных томов будут по-прежнему копироваться в облако для резервного копирования, чтобы облако можно было использовать для мобильности данных и аварийного восстановления. Кроме того, можно изменить тип тома (то есть преобразовать многоуровневые тома в локально закрепленные, и наоборот). 

- **Улучшения виртуального устройства StorSimple** — ранее в серии StorSimple 8000 виртуальные устройства позиционировались как решение аварийного восстановления или разработки и тестирования. Существовала только одна модель виртуального устройства (модель 1100). Обновление 2 вводит две модели виртуального устройства:

     - 8010 (прежнее название 1100) — без изменений; имеет емкость 30 ТБ и использует стандартное хранилище Azure.
     - 8020 — имеет емкость 64 ТБ и использует хранилище Azure класса Premium для повышения производительности.

    В обеих моделях (8010/8020) используется один виртуальный жесткий диск. При первом запуске виртуального устройства оно определяет параметры платформы и применяет соответствующую версию модели.

- **Улучшения сети** — обновление 2 содержит следующие улучшения сети:

     - Для облака можно включить несколько сетевых адаптеров, чтобы при сбое адаптера можно было выполнить отработку отказа.
     - Улучшения маршрутизации с исправлениями метрики для блоков с поддержкой облака.
     - Интерактивный повторный запрос ресурсов перед отработкой отказа.
     - Новые предупреждения при сбое службы.

- **Улучшения обновления** — в обновлении 1.2 и более ранних версий устройства серии StorSimple 8000 обновлялись по двум каналам: Центр обновления Windows для кластеризации, iSCSI и т. д. и Центр обновления Майкрософт для двоичных файлов и встроенного ПО. Обновление 2 использует Центр обновления Майкрософт для всех пакетов обновления. Это должно привести к сокращению времени на применение исправлений или отработку отказа.

- **Обновления встроенного ПО** — включены следующие обновления встроенного ПО:
    - LSI: lsi\_sas2.sys Версия продукта 2.00.72.10
    - Только SSD (без обновления HDD): XMGG, XGEG, KZ50, F6C2 и VR08

- **Упреждающая поддержка**: обновление 2 позволяет корпорации Майкрософт получить дополнительную диагностическую информацию от устройства. При обнаружении нашей оперативной командой проблем на устройствах мы имеем более широкие возможности для сбора информации с устройства и диагностики проблем. **Принимая обновление 2, вы позволяете нам предоставить данную поддержку**.
 

## Проблемы, устраненные в обновлении 2

В приведенной ниже таблице в общих чертах описаны проблемы, устраненные в обновлении 2.

| Нет. | Функция | Проблема | Для физического устройства | Для виртуального устройства |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Сетевые интерфейсы | После обновления до обновления 1 служба диспетчера StorSimple сообщила о сбое портов Data2 и Data3 на одном контроллере. Теперь эта проблема устранена. | Да | Нет |
| 2 | Обновления | Когда в прошлом завершалось обновление до обновления 1, на классическом портале Azure на нескольких устройствах звучали звуковые оповещения. Теперь эта проблема устранена. | Да | Нет |
| 3 | Проверка подлинности Openstack | При использовании Openstack в качестве поставщика облачных служб может появиться сообщение об ошибке, говорящее о том, что строка облачной проверки подлинности слишком длинная. Эта ошибка исправлена. | Да | Нет |


## Известные проблемы в обновлении 2

В таблице ниже содержится сводка по известным проблемам в этом выпуске.

| Нет. | Функция | Проблема | Комментарии и возможные решения | Для физического устройства | Для виртуального устройства |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Дисковый кворум | В редких случаях, если большинство дисков в корпусе EBOD устройства 8600 отключены, что приводит к отсутствию дискового кворума, пул хранения будет отключен. Он будет оставаться отключенным даже если диски будут подключены повторно. | Устройство необходимо будет перезагрузить. Если проблема возникнет снова, обратитесь в службу поддержки Майкрософт. | Да | Нет |
| 2 | Неправильный идентификатор контроллера | При замене контроллера, контроллер 0 может отображаться как контроллер 1. Во время замены контроллера, когда образ загружается с однорангового узла, идентификатор контроллера может изначально отображаться как идентификатор контроллера однорангового узла. Иногда такое поведение может также наблюдаться после перезагрузки системы. | Никаких действий от пользователя не требуется. Ситуация разрешится сама после завершения замены контроллера. | Да | Нет |
| 3 | учетные записи хранения; | Использование службы хранилища для удаления учетной записи хранения не поддерживается. Это приведет к ситуации, в которой невозможно получить пользовательские данные.| | Да | Да |
| 4\. | Отработка отказа устройства | Многократные отработки отказов контейнера томов из одного исходного устройства на разные целевые устройства не поддерживаются. Отработка отказа с одного неиспользуемого устройства на несколько устройств приведет к тому, что контейнеры томов на первом отказавшем устройстве потеряют право собственности на данные. После такой отработки отказа эти контейнеры томов при просмотре на классическом портале Azure отображаются или ведут себя по-другому. | | Да | Нет |
| 5 | Установка | Во время установки адаптера StorSimple для SharePoint для успешного завершения установки необходимо указать IP-адрес устройства. | | Да | Нет |
| 6 | Веб-прокси | Если в конфигурации веб-прокси указан протокол HTTPS, это повлияет на связь между устройством и службой и устройство будет отключено. В этом процессе также могут создаваться пакеты поддержки, вследствие чего устройство потребляет значительные ресурсы. | Убедитесь, что для URL-адреса прокси-сервера указан протокол HTTP. Дополнительные сведения см. в статье [Настройка прокси-сервера доступа в Интернет для устройства](storsimple-configure-web-proxy.md). | Да | Нет |
| 7 | Веб-прокси | Если настроить и включить веб-прокси на зарегистрированном устройстве, на этом устройстве необходимо будет перезагрузить активный контроллер. | | Да | Нет |
| 8 | Высокая задержка при связи с облаком и высокая рабочая нагрузка операций ввода-вывода | Когда на устройстве StorSimple обнаруживается очень высокая задержка для облачных служб (порядка нескольких секунд) в сочетании с высокой рабочей нагрузкой операций ввода-вывода, тома на устройстве переходят в состояние сниженной производительности и операции ввода-вывода могут завершиться ошибкой "Устройство не готово". | Чтобы исправить ошибку, потребуется вручную перезагрузить контроллеры устройства или выполнить отработку отказа устройства. | Да | Нет |
| 9 | Azure PowerShell | При использовании командлета StorSimple **Get-AzureStorSimpleStorageAccountCredential | Select-Object -First 1 -Wait** для выбора первого объекта с целью создания нового объекта **VolumeContainer** командлет возвращает все объекты. | Заключите командлет в круглые скобки следующим образом: **(Get-Azure-StorSimpleStorageAccountCredential) | Select-Object -First 1 -Wait** | Да | Да |
| 10| Миграция | При передаче нескольких контейнеров томов для миграции ETA для последней резервной копии имеет точное значение только для первого контейнера томов. Кроме того, после миграции первых 4 резервных копий в первом контейнере томов начинается параллельная миграция. | Рекомендуется переносить не более одного контейнера томов одновременно. | Да | Нет |
| 11| Миграция | После восстановления не происходит добавления томов в политику архивирования или в группу виртуального диска. | Для создания резервных копий вам придется самостоятельно добавлять эти тома в политику архивирования. | Да | Да |
| 12| Миграция | После завершения миграции устройство серии 5000 или 7000 не должно получать доступ к контейнерам с данными, для которых выполнена миграция. | После переноса и фиксации рекомендуется удалить контейнеры c данными, для которых выполнена миграция. | Да | Нет |
| 13\.| Клонирование и аварийное восстановление | Устройство StorSimple, на котором установлено обновление 1, не способно выполнять клонирование или аварийное восстановление на устройство под управлением программного обеспечения без обновления 1. | Чтобы использовать эти операции, установите обновление 1 на целевое устройство. | Да | Да |
| 14\. | Миграция | Резервное копирование конфигурации для миграции на устройстве серий 5000–7000 может завершиться с ошибкой, если имеются группы томов, в которых нет сопоставленных томов. | Удалите все пустые группы томов, в которых нет сопоставленных томов, и еще раз запустите операцию резервного копирования конфигурации.| Да | Нет |
| 15 | Командлеты Azure PowerShell и локально закрепленные тома | Создать локально закрепленный том с помощью командлетов Azure PowerShell невозможно. (Любой том, который создан с помощью Azure PowerShell, будет многоуровневым.) |Всегда используйте службу диспетчера StorSimple для настройки локально закрепленных томов.| Да | Нет |
| 16 |Пространство, доступное для локально закрепленных томов | При удалении локально закрепленного тома пространство для новых томов может обновляться не сразу. Служба диспетчера StorSimple обновляет доступное локальное пространство с частотой примерно один раз в час.| Подождите в течение часа, прежде чем приступать к созданию нового тома. | Да | Нет |
| 17 | Локально закрепленные тома | Задание восстановления предоставляет временный моментальный снимок базы данных в каталоге резервного копирования, но только на время выполнения этого задания. Кроме того, оно предоставляет группу виртуального диска с префиксом **tmpCollection** на странице **Политики резервного копирования**, но только во время выполнения этого задания. | Это может происходить, если задание восстановления включает только локально закрепленные тома или комбинацию локально закрепленных и многоуровневых томов. Если задание восстановления включает только многоуровневые тома, этого не произойдет. Вмешательство пользователя не требуется. | Да | Нет |
| 18 | Локально закрепленные тома | Если задание восстановления будет отменено, а сразу после этого произойдет отработка отказа контроллера, задание восстановления получит статус **Сбой**, а не **Отменено**. Если задание восстановления завершится сбоем, а сразу после этого произойдет отработка отказа контроллера, задание восстановления получит статус **Отменено**, а не **Сбой**. | Это может происходить, если задание восстановления включает только локально закрепленные тома или комбинацию локально закрепленных и многоуровневых томов. Если задание восстановления включает только многоуровневые тома, этого не произойдет. Вмешательство пользователя не требуется. | Да | Нет |
| 19 |Локально закрепленные тома | В случае отмены задания восстановления или сбоя восстановления с последующей отработкой отказа на странице **Задания** появится дополнительное задание восстановления. | Это может происходить, если задание восстановления включает только локально закрепленные тома или комбинацию локально закрепленных и многоуровневых томов. Если задание восстановления включает только многоуровневые тома, этого не произойдет. Вмешательство пользователя не требуется. | Да | Нет |
| 20 |Локально закрепленные тома | Если при попытке преобразовать многоуровневый том (созданный и клонированный с обновлением 1.2 или более ранней версии) в локально закрепленный том на устройстве не хватает места или облако недоступно, клоны могут быть повреждены.| Эта проблема возникает только с томами, которые были созданы и клонированы с помощью программного обеспечения до версии 2. Этот сценарий не должен встречаться часто.|
| 21 | Преобразование тома | Не обновляйте записи контроля доступа, связанные с томом, во время преобразования тома (из многоуровневого в локально закрепленный или наоборот). Обновление записей контроля доступа может привести к повреждению данных. | При необходимости обновите записи контроля доступа до преобразования тома и не выполняйте дальнейших обновлений записей контроля доступа во время выполнения преобразования. |

## Обновления контроллера и встроенного ПО в обновлении 2

Этот выпуск обновляет драйвер и встроенное ПО диска на устройстве.
 
- Дополнительные сведения об обновлении встроенного ПО LSI см. в статье 3121900 базы знаний Майкрософт. 
- Дополнительные сведения об обновлении встроенного ПО диска см. в статье 3121899 Базы знаний Майкрософт.
 
## Обновления для виртуальных устройств в обновлении 2

Это обновление не может применяться к виртуальному устройству. Нужно будет создать новые виртуальные устройства.

## Дальнейшие действия

Узнайте, как [установить обновление 2](storsimple-install-update-2.md) на устройство StorSimple.

<!---HONumber=AcomDC_0218_2016-->