<properties
   pageTitle="Создание среды тестирования производительности Elasticsearch | Microsoft Azure"
   description="Здесь описано, как настроить среду для тестирования производительности кластера Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Создание среды тестирования производительности Elasticsearch в Azure

Этот материал входит в [цикл статей, посвященных вопросам Elasticsearch](guidance-elasticsearch.md).

В этом документе описано, как настроить среду для тестирования производительности кластера Elasticsearch. Эта конфигурация использовалась для тестирования производительности приема данных и рабочих нагрузок запросов, как описано в статье [Tuning Data Ingestion Performance for Elasticsearch on Azure][] (Настройка производительности приема данных для Elasticsearch в Azure).

Тестирование производительности выполнено с помощью приложения [Apache JMeter](http://jmeter.apache.org/) в сочетании со [стандартным набором](http://jmeter-plugins.org/wiki/StandardSet/) подключаемых модулей, в конфигурации которых использовались специально настроенные виртуальные машины (вне кластера Elasticsearch) с разделением по типу главный-подчиненный.

На каждом узле Elasticsearch установлен [PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/). В следующих разделах приведены указания по повторному созданию тестовой среды, после чего можно самостоятельно проверить производительность с помощью JMeter. В этих указаниях предполагается, что вы уже создали кластер Elasticsearch, для подключения узлов которого использовалась виртуальная сеть Azure.

Обратите внимание, что в рамках тестовой среды запущен ряд виртуальных машин Azure, управляемых с помощью одной группы ресурсов Azure.

Чтобы упростить мониторинг и анализ внутренних процессов кластера Elasticsearch, мы также установили и настроили [Marvel](https://www.elastic.co/products/marvel). Если статистические данные JMeter свидетельствуют о пиках и уменьшении производительности, с помощью этого инструмента можно получить ценную информацию, чтобы определить причины отклонений.

На следующем изображении показана структура всей системы.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Обратите внимание на следующие моменты.

- Главная виртуальная машина JMeter работает под управлением Windows Server и обеспечивает возможность использования графического пользовательского интерфейса для консоли JMeter. Главная виртуальная машина JMeter также предоставляет графический пользовательский интерфейс (приложение *jmeter*), в котором можно использовать тестировщик, чтобы создавать и выполнять тесты, а также визуализировать результаты. Эта виртуальная машина взаимодействует с виртуальными машинами JMeter Server, с которых фактически отправляются запросы на выполнение тестов.

- Подчиненные виртуальные машины JMeter работают под управлением Ubuntu Server (Linux). Для их использования не нужен графический пользовательский интерфейс. На виртуальных машинах JMeter Server работает соответствующее программное обеспечение (приложение *jmeter-server*), позволяющее отправлять запросы в кластер Elasticsearch.

- В процедуре не применялись выделенные клиентские узлы, но использовались главные узлы.

- Количество узлов данных в кластере зависит от тестируемого сценария.

- Для мониторинга производительности на всех узлах кластера Elasticsearch используется Marvel, а JMeter Server Agent собирает данные мониторинга для последующего анализа.

- Если выполняется тестирование Elasticsearch 2.0.0 и более поздней версии, на одном из узлов данных также работает Kibana. Это обязательное требование для всех версий Marvel, в работе которых используется Elasticsearch 2.0.0 и более поздней версии.

## Создание группы ресурсов Azure для виртуальных машин

Чтобы осуществлять сбор данных производительности, главная машина JMeter должна быть напрямую подключена к каждому узлу кластера Elasticsearch. Если виртуальная сеть JMeter отличается от виртуальной сети кластера Elasticsearch, для каждого узла Elasticsearch будет настроен общедоступный IP-адрес. При возникновении проблем с конфигурацией Elasticsearch попробуйте подключить виртуальные машины JMeter к той же виртуальной сети, что и кластер Elasticsearch, используя ту же группу ресурсов. В этом случае первую процедуру можно не выполнять.

Сначала [создайте группу ресурсов](../articles/resource-group-portal/#create-resource-group-and-resources). В этом документе предполагается, что группа ресурсов называется *JMeterPerformanceTest*. Если вы хотите, чтобы виртуальные машины JMeter работали в той же виртуальной сети, что и кластер Elasticsearch, используйте его группу ресурсов вместо создания новой.

## Создание главной виртуальной машины JMeter

Затем [создайте виртуальную машину Windows](../articles/virtual-machines-windows-tutorial/) на основе образа *Windows Server 2008 R2 SP1*. Мы рекомендуем выбрать для виртуальной машины размер, предусматривающий достаточно ядер и памяти для тестирования производительности. В идеале у этого компьютера должно быть по крайней мере 2 ядра и 3,5 ГБ ОЗУ (Standard A2 или больше).

<!-- TODO add info on why disabling diagnostics is positive -->

Мы рекомендуем отключить диагностику. При создании виртуальной машины на портале это можно сделать в колонке *Параметры*, выбрав *Мониторинг* в разделе *Диагностика*. Для остальных параметров оставьте значения по умолчанию.

Убедитесь, что виртуальная машина и связанные с ней ресурсы успешно созданы. Для этого [проверьте группу ресурсов](../articles/resource-group-portal/#browse-resource-groups) на портале. Перечисленные ресурсы должны включать в себя виртуальную машину, группу безопасности сети и общедоступный IP-адрес (у всех них должно быть одинаковое имя), а также сетевой интерфейс и учетную запись хранения с именами, за основу которых должны быть взяты имена соответствующих ресурсов виртуальной машины.

## Создание подчиненных виртуальных машин JMeter

Теперь [создайте виртуальную машину Linux](../articles/virtual-machines-linux-tutorial-portal-rm/) на основе образа *Ubuntu Server 14.04 LTS*. Как и для главной виртуальной машины JMeter, мы рекомендуем выбрать для этого компьютера размер, предусматривающий достаточно ядер и памяти для тестирования производительности. В идеале у него должно быть 2 ядра и 3,5 ГБ ОЗУ (Standard A2 или больше).

Мы также рекомендуем отключить диагностику.

Вы можете создать любое количество подчиненных виртуальных машин.

## Установка JMeter Server на подчиненных виртуальных машинах JMeter

Подчиненные виртуальные машины JMeter работают под управлением Linux и по умолчанию к ним нельзя подключиться, установив подключение к удаленному рабочему столу. Вместо этого вы можете [использовать PuTTY, чтобы открыть окно командной строки,](../articles/virtual-machines-linux-how-to-log-on/) на каждой виртуальной машине.

После подключения к одной из подчиненных виртуальных машин мы установим JMeter, используя командную строку Bash.

Сначала установите среду выполнения Java. Она необходима для работы JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Затем скачайте программное обеспечение JMeter в виде ZIP-файла.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Установите команду unzip, а затем используйте ее для развертывания программного обеспечения JMeter. Программное обеспечение скопируется в папку с именем **apache-jmeter-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Измените каталог *bin*, где содержатся исполняемые файлы JMeter, и сделайте программы *jmeter-server* и *jmeter* исполняемыми.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

Теперь нужно изменить файл `jmeter.properties` в текущей папке (используйте для этого наиболее привычный текстовый редактор, например *vi* или *vim*). Найдите следующие строки:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Раскомментируйте (удалите начальные символы "##") и измените строки, как показано ниже, а затем сохраните файл и закройте редактор:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Теперь выполните следующие команды, чтобы входящий трафик TCP мог проходить через порт 4441 (прослушивание которого вы недавно настроили для *jmeter-server*):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Скачайте ZIP-файл, в котором содержится стандартная коллекция подключаемых модулей JMeter (они обеспечивают использование счетчиков мониторинга производительности), а затем распакуйте файл. При распаковке файла в этом расположении подключаемые модули будут помещены в нужную папку.

Если отобразится запрос на замену файла LICENSE, нажмите клавишу A (для всех):

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Используйте `nohup`, чтобы запустить JMeter Server в фоновом режиме. В ответ должен отобразиться идентификатор процесса и сообщение о том, что сервер создал удаленный объект и готов к получению команд.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Если работа виртуальной машины завершалась, это значит, что работа программы JMeter Server прервана. В таком случае необходимо подключиться к виртуальной машине и перезапустить ее вручную. Кроме того, вы можете настроить автоматическое выполнение команды *jmeter-server* при запуске системы. Для этого добавьте следующие команды в файл `/etc/rc.local` (перед командой *exit 0*):

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Замените `<username>` на свое имя пользователя.

Окно терминала можно оставить открытым, чтобы отслеживать работу JMeter Server во время тестирования.

Эти шаги необходимо повторить для каждой подчиненной виртуальной машины JMeter.

## Установка агента JMeter Server Agent на узлах Elasticsearch

Эта процедура предполагает, что у вас есть доступ для входа на узлы Elasticsearch. Если вы создали кластер на основе шаблона ARM, можно использовать виртуальную машину, выполняющую функцию "поля перехода", чтобы подключиться к каждому узлу, как показано в разделе [о топологии рабочей среды Azure](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies). К этой виртуальной машине также можно подключиться с помощью PuTTY.

Чтобы войти на каждый узел кластера Elasticsearch, можно использовать команду *ssh*.

Войдите на один из узлов Elasticsearch, используя учетные данные администратора. В командной строке Bash введите следующие команды, чтобы создать папку для хранения JMeter Server Agent, а затем перейдите к этой папке:

```bash
mkdir server-agent
cd server-agent
```

Выполните следующие команды, чтобы установить команду *unzip* (если она не установлена), скачайте программное обеспечение JMeter Server Agent и распакуйте его:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Выполните следующую команду, чтобы настроить брандмауэр и направить трафик TCP через порт 4444 (этот порт использует JMeter Server Agent):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Выполните следующую команду, чтобы запустить JMeter Server Agent в фоновом режиме:

```bash
nohup ./startAgent.sh &
```

JMeter Server Agent должен вернуть ответ с сообщениями о том, что он запущен и прослушивает порт 4444. Нажмите клавишу ВВОД, чтобы открыть окно командной строки, а затем выполните следующую команду. Замените `<nodename>` на имя узла. Если вы не уверены в правильности имени узла, его можно найти, выполнив команду `hostname`:

```bash
telnet <nodename> 4444
```

Эта команда позволяет установить telnet-подключение к порту 4444 на локальном компьютере. Это подключение можно использовать, чтобы убедиться, что JMeter Server Agent работает правильно.

Если JMeter Server Agent не запущен, вы получите такой ответ:

`*telnet: Unable to connect to remote host: Connection refused*.`

Если JMeter Server Agent работает и порт 4444 настроен правильно, вы увидите такой ответ:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] Во время сеанса telnet-подключения не отображаются никакие запросы.

Введите следующую команду во время сеанса telnet-подключения:

``` 
test
```

Если JMeter Server Agent настроен и правильно ведет прослушивание, он должен уведомить о том, что получил команду, и вернуть сообщение *Yep*.

> [AZURE.NOTE] Чтобы получить данные мониторинга производительности, можно использовать и другие команды. Например, команда `metric-single:cpu:idle` позволяет узнать текущее время бездействия ЦП (это моментальный снимок). Чтобы просмотреть весь список команд, посетите страницу [PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/).

Во время telnet-сеанса можно ввести следующую команду, чтобы выйти из него и вернуться к командной строке Bash:

``` 
exit
```

> [AZURE.NOTE] Если вы выйдете или завершите работу компьютера и перезапустите его, JMeter Server Agent нужно будет перезагрузить вручную, подобно подчиненной виртуальной машине JMeter, выполнив команду `startAgent.sh`. Если необходимо настроить автоматический запуск JMeter Server Agent, добавьте следующую команду в конец файла `/etc/rc.local` перед командой *exit 0*. Замените `<username>` на свое имя пользователя:

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Замените `<username>` на свое имя пользователя.

Теперь вы можете либо повторить всю процедуру для каждого узла в кластере Elasticsearch, либо скопировать папку server-agent и ее содержимое для каждого узла, использовав команду `scp`, а затем выполнить команду `ssh`, чтобы запустить JMeter Server Agent, как показано ниже. Замените `<username>` на свое имя пользователя, а `<nodename>` — на имя узла, куда нужно скопировать и где нужно запускать программное обеспечение (при выполнении команд может отображаться запрос на ввод пароля):

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Установка и настройка JMeter на главной виртуальной машине JMeter

На портале Azure щелкните *Группы ресурсов*. В колонке *Группы ресурсов* щелкните ту группу ресурсов, к которой принадлежат главная и подчиненные виртуальные машины JMeter. В колонке *Группа ресурсов* щелкните главную виртуальную машину JMeter. В колонке виртуальной машины на панели инструментов щелкните *Подключить*. Откройте RDP-файл, если в веб-браузере отобразится запрос. Windows создаст удаленное подключение рабочего стола к виртуальной машине. Когда отобразится запрос, введите имя и пароль пользователя виртуальной машины.

В виртуальной машине в Internet Explorer перейдите на страницу [Загрузить Java для Windows](http://www.java.com/en/download/ie_manual.jsp). Следуйте указаниям, чтобы скачать и запустить установщик Java.

В веб-браузере перейдите на страницу [Download Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) (Скачать Apache JMeter) и скачайте ZIP-файл, в котором содержатся самые последние двоичные данные. Сохраните ZIP-файл в удобном расположении в виртуальной машине.

Перейдите на веб-сайт [с настраиваемыми подключаемыми модулями JMeter](http://jmeter-plugins.org/) и скачайте стандартный набор подключаемых модулей. Сохраните ZIP-файл в той же папке, куда вы скачали JMeter на предыдущем шаге.

В проводнике перейдите к папке с ZIP-файлом apache-jmeter-*xx* (где *xx* — это текущая версия JMeter), а затем извлеките файлы в текущую папку (при распаковке ZIP-файла будет создана вложенная папка с именем apache-jmeter-*xxx*).

Извлеките файлы из JMeterPlugins-Standard-*yyy*.zip (где *yyy* — это текущая версия подключаемых модулей) в папку apache-jmeter-*xxx*. В результате подключаемые модули будут добавлены в нужную папку JMeter (вы можете свободно объединять папки библиотеки, а также перезаписывать файлы лицензий и сведений при появлении соответствующего запроса).

Перейдите к папке apache-jmeter-*xxx*/bin и внесите изменения в файл jmeter.properties, используя Блокнот. В файле `jmeter.properties` найдите раздел *Remote hosts and RMI configuration*. В этом разделе файла найдите следующую строку:

```yaml
remote_hosts=127.0.0.1
```

Измените эту строку и замените IP-адрес 127.0.0.1 на разделенный запятыми список IP-адресов или имен узлов для каждого подчиненного сервера JMeter. Например:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Найдите следующую строку, удалите символ `#` в начале, а затем измените значения параметров client.rmi.localport:

```yaml
#client.rmi.localport=0
```

на:

```yaml
client.rmi.localport=4440
```

Сохраните файл и закройте Блокнот.

На панели инструментов Windows нажмите кнопку *Пуск*, выберите *Администрирование*, а затем щелкните *Брандмауэр Windows в режиме повышенной безопасности*. В окне *Брандмауэр Windows в режиме повышенной безопасности* в левой области щелкните правой кнопкой мыши *Правила для входящих подключений* и нажмите кнопку *Создать правило*.

В окне *Мастер* *создания правила для нового входящего подключения* на странице *Тип правила* выберите *Порт* и нажмите кнопку *Далее*. На странице *Протоколы и порты* выберите *TCP*, а затем — *Определенные локальные порты*, в текстовом поле введите `4440-4444` и нажмите кнопку *Далее*. На странице *Действие* выберите *Разрешить подключение* и нажмите кнопку *Далее*. На странице *Профиль* оставьте все установленные флажки и нажмите кнопку *Далее*. На странице *Имя* в текстовом поле *Имя* введите *JMeter*, а затем нажмите кнопку *Готово*. Закройте окно *Брандмауэр Windows в режиме повышенной безопасности*.

В проводнике в папке apache-jmeter-*xx*/bin дважды щелкните пакетный файл Windows *jmeter*, чтобы запустить графический пользовательский интерфейс. Отобразится пользовательский интерфейс:

![](./media/guidance-elasticsearch/performance-image17.png)

В строке меню щелкните меню *Run* (Запуск), а затем выберите пункт *Remote Start* (Запустить удаленно) и убедитесь, что указаны два подчиненных компьютера JMeter:

![](./media/guidance-elasticsearch/performance-image18.png)

Теперь все готово для запуска тестирования производительности.

## Установка и настройка Marvel

Шаблон быстрого запуска Elasticsearch для Azure автоматически установит и настроит соответствующую версию Marvel, если при создании кластера вы установили значение true для параметров MARVEL и KIBANA:

![](./media/guidance-elasticsearch/performance-image19.png)

При добавлении Marvel в существующий кластер установку необходимо выполнить вручную. Этот процесс зависит от используемой версии Elasticsearch, 1.7.x или 2.x, как описано в следующих процедурах.

### Установка Marvel для Elasticsearch 1.7.3 или более ранней версии

Если вы используете Elasticsearch 1.7.3 или более ранней версии, выполните следующие действия *на каждом узле* в кластере:

- Войдите на узел и перейдите к домашнему каталогу Elasticsearch. Обычно в Linux корневой каталог — `/usr/share/elasticsearch`.

-  Выполните следующую команду, чтобы скачать и установить подключаемый модуль Marvel для Elasticsearch.

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Остановите и перезапустите Elasticsearch на узле:

```bash
sudo service elasticsearch restart
```

- Чтобы убедиться в правильности установки Marvel, откройте браузер и перейдите по URL-адресу `http://<server>:9200/_plugin/marvel`. Замените `<server>` на имя или IP-адрес любого сервера Elasticsearch в кластере. Убедитесь, что отображается страница, аналогичная следующей:

![](./media/guidance-elasticsearch/performance-image20.png)


### Установка Marvel для Elasticsearch 2.0.0 или более поздней версии

Если вы используете Elasticsearch 2.0.0 или более поздней версии, выполните следующие задачи *на каждом узле* в кластере.

Войдите на узел и перейдите к домашнему каталогу Elasticsearch (обычно это `/usr/share/elasticsearch`). Выполните следующие команды, чтобы скачать и установить подключаемый модуль Marvel для Elasticsearch.

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Остановите и перезапустите Elasticsearch на узле:

```bash
sudo service elasticsearch restart
```

В следующей процедуре замените `<kibana-version>` на 4.2.2, если вы используете Elasticsearch 2.0.0 или 2.0.1, или на 4.3.1, если используете Elasticsearch 2.1.0 или более поздней версии. Замените `<marvel-version>` на 2.0.0, если вы используете Elasticsearch 2.0.0 или 2.0.1, или на 2.1.0, если используете Elasticsearch 2.1.0 или более поздней версии. Выполните следующие задачи *на одном из узлов* кластера:

Войдите на узел и скачайте соответствующую сборку Kibana для используемой версии Elasticsearch с [веб-сайта загрузок Elasticsearch](https://www.elastic.co/downloads/past-releases), а затем извлеките содержимое пакета:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Откройте порт 5601 для приема входящих запросов:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Перейдите к папке файлов конфигурации Kibana (`kibana-<kibana-version>-linux-x64/config`), внесите изменения в файл `kibana.yml` и добавьте следующую строку. Замените `<server>` на имя или IP-адрес сервера Elasticsearch в кластере:

```yaml
elasticsearch.url: "http://<server>:9200"
```

Перейдите к папке bin Kibana (`kibana-<kibana-version>-linux-x64/bin`) и выполните следующую команду, чтобы интегрировать подключаемый модуль Marvel в Kibana:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Запустите Kibana:

```bash
sudo nohup ./kibana &
```

Чтобы проверить установлен ли Marvel, откройте браузер и перейдите по URL-адресу `http://<server>:5601/app/marvel`. Замените `<server>` на имя или IP-адрес сервера под управлением Kibana.

Убедитесь, что отображаемая страница аналогична показанной ниже (имя кластера может отличаться от показанного на рисунке).

![](./media/guidance-elasticsearch/performance-image21.png)

Щелкните ссылку на свой кластер (на рисунке выше это elasticsearch210). Должна отобразиться страница, аналогичная следующей:

![](./media/guidance-elasticsearch/performance-image22.png)

[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->