<properties
   pageTitle="Использование docker-machine в Azure | Microsoft Azure"
   description="Показывает, как наладить работу машины Docker под управлением Ubuntu в Azure с использованием классической модели развертывания."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Использование docker-machine в Azure

В этом разделе описано, как использовать [Docker](https://www.docker.com/) с [машиной](https://github.com/docker/machine) и [CLI Azure](https://github.com/Azure/azure-xplat-cli), чтобы создать виртуальную машину Azure для быстрого и легкого управления контейнерами Linux с компьютера под управлением Ubuntu. Для демонстрации в учебнике показано, как развернуть [образ Docker Hub для BusyBox](https://registry.hub.docker.com/_/busybox/) и [образ Docker Hub для nginx](https://registry.hub.docker.com/_/nginx/), а также настроить контейнер для маршрутизации веб-запросов в контейнер nginx. (В документации по **машине** Docker описано, как изменить эти указания для других платформ.)


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


Есть некоторые предварительные требования к изучению этого учебника. Кроме этого, понадобится установить следующее ПО.

1. [npm](https://docs.npmjs.com/) и [Node.js](http://nodejs.org/).
2. [CLI Azure.](https://github.com/Azure/azure-xplat-cli)
3. [Клиент Docker](https://docs.docker.com/installation/).

Если вы установите все эти элементы в указанном порядке, ваш компьютер под управлением Ubuntu будет готов к работе по учебнику. (Указания в этом учебнике должны во многом совпадать с шагами аналогичного процесса для любого другого дистрибутива на основе dpkg, например Debian. Сообщите нам в комментариях, если обнаружите дополнительные требования или шаги.)

## Получение docker-machine или ее создание

Самый быстрый способ начать работу с **docker-machine** — скачать соответствующий выпуск непосредственно со [страницы общего доступа к выпуску](https://github.com/docker/machine/releases). В этом учебнике упоминался клиентский компьютер с архитектурой x64 под управлением Ubuntu, поэтому использовался образ **docker-machine\_linux-amd64**.

Вы также можете самостоятельно создать **docker-machine**, выполнив следующие шаги для [увеличения возможностей машины](https://github.com/docker/machine#contributing). Чтобы создать машину, необходимо пространство для скачивания объемом 1 ГБ, но в ходе создания вы можете настроить машину в соответствии со своими требованиями.

> [AZURE.NOTE] Можно также создать [символьную ссылку](http://en.wikipedia.org/wiki/Symbolic_link) на версию ее платформы, но в этом учебнике используется непосредственно имя двоичного файла, чтобы продемонстрировать точное поведение. В результате вместо такой команды, как `docker-machine env`, показанной в документации к **docker-machine**, в этом учебнике используется `docker-machine_linux-amd64 env`. Независимо от того, что вы выберите — создать символическую ссылку или непосредственно использовать имя двоичного файла, при изменении используемого имени необходимо изменить имя в приведенных ниже указаниях.

<br />

>  Независимо от выбранного метода необходимо вызвать имя двоичного файла непосредственно в командной строке или указать путь к двоичному файлу, например **/usr/local/bin**. Убедитесь, что он помечен как исполняемый файл. Для этого введите `chmod +x` &lt;*`binaryName`*&gt;, где &lt;*`binaryName`*&gt; — это имя исполняемого файла компьютера Docker. В этом учебнике используется **docker-machine\_linux-amd64**.

## Создание файлов сертификата и ключа для Docker, машины и Azure

Теперь необходимо создать файлы сертификата и ключа, требуемые Azure для подтверждения подлинности вашего удостоверения и разрешений, которые также нужны **docker-machine**, чтобы взаимодействовать с вашей виртуальной машиной Azure для создания контейнеров и удаленного управления ими. Если вы уже создали эти файлы в каталоге (вероятно, чтобы использовать с Docker), их можно использовать снова. Однако для тестирования **docker-machine** рекомендуем создать их в отдельном каталоге и указать в docker-machine.

> [AZURE.NOTE] В случае повторных попыток создать **docker-machine** убедитесь, что вы используете те же самые файлы сертификата и ключа. **docker-machine** также создает набор клиентских сертификатов. Все, что она создает, можно проверить в `~/.docker/machine`. При перемещении этих сертификатов на другой компьютер также необходимо переместить папки сертификатов **docker-machine**. Ситуация отличается, если вы собираетесь использовать **docker-machine** на другой платформе, например, только чтобы увидеть, как она работает.

Если у вас уже есть опыт использования дистрибутивов Linux, возможно, в определенном расположении на вашем компьютере уже есть эти файлы и вы можете их использовать. Кроме того, в [документации по защите Docker с помощью протоколов HTTPS также описаны подробные шаги](https://docs.docker.com/articles/https/). Тем не менее, ниже приведена простейшая процедура.

1. Создайте локальную папку, перейдите к этой папке в командной строке и введите:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Будьте готовы ввести здесь пароль экспорта для сертификата и сохранить этот пароль для дальнейшего использования. Затем введите следующую команду:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Загрузите свой CER-файл сертификата на портале Azure. На [классическом портале Azure](https://manage.windowsazure.com) щелкните **Параметры** в левом нижнем углу области обслуживания (как показано ниже).

	![][portalsettingsitem]

	Щелкните **Сертификаты управления**:

	![][managementcertificatesitem]

	Затем выберите ссылку **Передать** (в нижней части страницы) ![][uploaditem], чтобы передать файл **mycert.cer**, который вы создали на предыдущем шаге.

3. В той же области **Параметры** на портале щелкните **Подписки** и создайте идентификатор подписки, который следует использовать при создании виртуальной машины, так как он будет использоваться на следующем шаге. (Идентификатор подписки можно также определить с помощью командной строки, выполнив команду `azure account list` в CLI Azure, которая отображает идентификатор подписки для каждой подписки вашей учетной записи.)

4. Создайте в Azure виртуальную машину, на которой вы разместите узел Docker, используя команду **docker-machine create**. Для выполнения этой команды требуется идентификатор подписки, созданный на предыдущем шаге, и путь к **PEM**-файлу, созданному на шаге 1. В этом разделе в качестве имени облачной службы Azure (и вашей виртуальной машины) используется имя machine-name, но его необходимо заменить выбранным вами именем. Помните, что на каждом шаге, в котором необходимо указывать имя виртуальной машины, нужно использовать имя вашей облачной службы. (Учтите, что в этом примере мы используем полное имя двоичного файла, а не символьную ссылку **docker-machine**.)

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Так как первые два шага требуют создания новой виртуальной машины, загрузки агента Linux для Azure, а также обновления новой виртуальной машины, вы увидите примерно следующее.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE] Переход в подготовленное состояние может длиться несколько минут, так как создается виртуальная машина. Во время ожидания можно проверить состояние нового узла Docker, вводя `azure vm list` в CLI Azure, пока не увидите свои виртуальные машины с состоянием **ReadyRole**.

5. Задайте переменные Docker и переменные среды машины для сеанса терминала. В последней строке отзыва вы должны незамедлительно запустить команду **env**, чтобы экспортировать переменные среды, необходимые для использования вашего клиента Docker непосредственно с определенного компьютера.

		$(docker-machine_linux-amd64 env machine-name)

	После этого вам не будут нужны специальные команды, чтобы использовать локальный клиент Docker для подключения к виртуальной машине, созданной **машиной** Docker.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE] В этом учебнике показано, как создать с помощью **docker-machine** одну виртуальную машину. Тем не менее, можно повторить шаги, чтобы создать столько машин, сколько вам нужно. В таком случае мы рекомендуем использовать команду **env** для переключения между виртуальными машинами с Docker, чтобы задать переменные среды **docker** для каждой отдельной команды. Например, чтобы использовать **docker info** для другой виртуальной машины, можно ввести `docker $(docker-machine env <VM name>) info`, после чего команда **env** заполнит информацию о подключении к Docker, которую следует использовать с этой виртуальной машиной.

## Теперь все готово. Запустим несколько приложений удаленно, используя Docker и образы из Docker Hub.

Теперь Docker можно использовать обычным способом — для создания приложений в контейнере. Проще всего продемонстрировать это на примере с [busybox](https://registry.hub.docker.com/_/busybox/).

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

Тем не менее, возможно, вы захотите создать приложение, которое можно мгновенно увидеть в Интернете, например [nginx](https://registry.hub.docker.com/_/nginx/) с помощью [Docker Hub](https://registry.hub.docker.com/).

> [AZURE.NOTE] Помните, что нужно использовать параметр **-P**, чтобы назначить случайные порты **docker** образу, и **-d** — чтобы обеспечить постоянную работу контейнера в фоновом режиме. (Если вы забыли об этом, при запуске nginx его работа тут же завершится. Помните об этом!)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Чтобы увидеть приложение в Интернете, создайте общедоступную конечную точку на порте 80 для виртуальной машины Azure и сопоставьте этот порт с портом контейнера nginx. Сначала используйте `docker ps -a`, чтобы определить контейнер и найти, какие порты **docker** назначены порту 80 контейнера. (Ниже отображается информация, измененная чтобы показать вам только информацию о порте. На самом деле вы увидите больше.)

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Можно увидеть, что Docker назначило порт 80 контейнера порту 49153 виртуальной машины. Теперь можно использовать CLI Azure для сопоставления внешнего общедоступного порта 80 облачной службы с портом 49153 виртуальной машины. После этого Docker обеспечит маршрутизацию входящего трафика TCP порта 49153 виртуальной машины в контейнер nginx.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Откройте свой браузер и убедитесь в этом.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
Перейдите к [руководству пользователя Docker](https://docs.docker.com/userguide/) и узнайте, как создавать некоторые приложения в Microsoft Azure. Вы также можете воспользоваться [Docker и Swarm в Azure] и увидеть, как можно использовать [Swarm](https://github.com/docker/swarm) с помощью Docker и Azure.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Docker и Swarm в Azure]: virtual-machines-docker-swarm.md

<!---HONumber=AcomDC_0128_2016-->