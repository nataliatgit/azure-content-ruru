<properties 
   pageTitle="Группы томов диспетчера моментальных снимков StorSimple | Microsoft Azure"
   description="Узнайте, как использовать оснастку консоли MMC ";Диспетчер моментальных снимков StorSimple"; для создания групп томов и управления ими."
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
   ms.date="12/02/2015"
   ms.author="v-sharos" />

# Создание групп томов и управление ими с помощью диспетчера моментальных снимков StorSimple

## Обзор

Узел **Группы томов** на панели **Область** позволяет назначить тома группам томов, просмотреть сведения о группе томов, запланировать операции архивации и внести изменения в группы томов.

Группы томов — это пулы связанных томов, которые используются для обеспечения согласованности резервных копий с приложениями. Дополнительные сведения см. в разделах [Томы и группы томов](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups), а также [Интеграция со службой теневого копирования томов Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT]
>
> * У всех томов в группе томов должен быть один поставщик облачной службы.
> 
> * Настраивая группы томов, не размещайте в одной группе томов общие тома кластера (CSV) и тома других типов. Диспетчер моментальных снимков StorSimple не поддерживает совместное использование общих томов кластера и томов других типов в одном моментальном снимке.
 
![Узел "Группы томов"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Рисунок 1. Узел "Группы томов" диспетчера моментальных снимков StorSimple**

В этом учебнике объясняется, как использовать диспетчер моментальных снимков StorSimple для выполнения таких задач:

- просмотр сведений о группах томов; 
- создание группы томов;
- архивация группы томов;
- внесение изменений в группу томов;
- удаление группы томов.

Все эти действия также можно выполнить на панели **Действия**.
 
## Просмотр групп томов

Если щелкнуть узел **Группы томов**, на панели **Результаты** отобразятся указанные ниже сведения о каждой группе томов в зависимости от того, какие столбцы вы выбрали. Столбцы на панели **Результаты** можно настраивать. (Щелкните правой кнопкой мыши узел **Тома**, а затем последовательно выберите пункты **Вид** и **Добавить или удалить столбцы**.)

Столбец "Результаты" | Описание 
:--------------|:------------ 
Имя | Столбец **Имя** содержит имя группы томов.
Приложение | В столбце **Приложения** указано количество модулей записи VSS, которые в настоящее время установлены и запущены на узле Windows.
Выбрано | В столбце **Выбрано** указывается количество томов в группе томов. Ноль (0) значит, что с томами в группе томов не связано ни одно приложение.
Импортировано | В столбце **Импортировано** отображается количество импортированных томов. Если задано значение **True**, этот столбец указывает, что группа томов импортирована из классического портала Azure, а не создана в диспетчере моментальных снимков StorSimple.
 
>[AZURE.NOTE]Группы томов диспетчера моментальных снимков StorSimple также отображаются на вкладке **Политики архивации** на классическом портале Azure.
 
## создание группы томов;

Выполните указанные ниже действия, чтобы создать группу томов.

#### Создание группы томов

1. Щелкните соответствующий значок на рабочем столе, чтобы запустить диспетчер моментальных снимков StorSimple. 

2. На панели **Область** щелкните правой кнопкой мыши **Группы томов** и выберите пункт **Создать группу томов**.

    ![Создание группы томов](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Откроется диалоговое окно **Создание группы томов**.

    ![Диалоговое окно "Создание группы томов"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)

3.  Введите следующие сведения:

    1. В поле **Имя** введите уникальное имя новой группы томов. 

    2. В поле **Приложения** выберите приложения, связанные с томами, которые будут добавлены в группу томов.

        В поле **Приложения** перечисляются только те приложения, которые используют тома Azure StorSimple и для которых активированы модули записи VSS. Модуль записи VSS включается, только если все тома, известные ему, представляют собой тома Azure StorSimple. Если оставить поле "Приложения" пустым, не будет установлено ни одно приложение, использующее тома Azure StorSimple и содержащее поддерживаемые модули записи VSS. (В настоящее время Azure StorSimple поддерживает Microsoft Exchange и SQL Server.) Дополнительные сведения о модулях записи VSS см. в разделе [Интеграция со службой теневого копирования томов Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        При выборе приложения будут автоматически выбраны все тома, связанные с ним. И наоборот, если выбрать тома, связанные с определенным приложением, приложение будет автоматически выбрано в поле **Приложения**.

    3. В поле **Тома** выберите тома Azure StorSimple, которые нужно добавить в группу томов.

      - Вы можете включить тома с одним или несколькими разделами. (Тома с несколькими разделами могут представлять собой динамические или основные диски с несколькими разделами.) Том с несколькими разделами рассматривается как единое целое. Таким образом, если добавить в группу томов только один из разделов, одновременно в нее будут автоматически добавлены все другие разделы. Том с несколькими разделами по-прежнему будет считаться единым целым даже после добавления в группу томов.

      - Вы можете создать пустые группы томов, не назначая им каких-либо томов.

      - Не размещайте в одной группе томов общие тома кластера (CSV) и тома других типов. Диспетчер моментальных снимков StorSimple не поддерживает совместное использование общих томов кластера (CSV) и томов других типов в одном моментальном снимке.

4. Нажмите кнопку **ОК**, чтобы сохранить группу томов.

## архивация группы томов;

Выполните указанные ниже действия, чтобы архивировать группу томов.

#### Архивация группы томов

1. Щелкните соответствующий значок на рабочем столе, чтобы запустить диспетчер моментальных снимков StorSimple.

2. На панели **Область** разверните узел **Группы томов**, щелкните правой кнопкой мыши имя группы томов и выберите пункт **Создать резервную копию**.

    ![Немедленная архивация группы томов](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. В диалоговом окне **Создание резервной копии** выберите пункт **Локальный моментальный снимок** или **Облачный моментальный снимок**, а затем нажмите кнопку **Создать**.

    ![Диалоговое окно "Создание резервной копии"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)

4. Чтобы убедиться, что архивация запущена, разверните узел **Задания** и щелкните **Выполняются**. Операция архивации должна быть указана в списке.

5. Чтобы просмотреть созданный моментальный снимок, последовательно разверните узел **Каталог архивов** и имя группы томов, а затем щелкните **Локальный моментальный снимок** или **Облачный моментальный снимок**. Если операция архивации успешно завершена, она будет указана в списке.

## Внесение изменений в группу томов

Выполните указанные ниже действия, чтобы внести изменения в группу томов.

#### Внесение изменений в группу томов

1. Щелкните соответствующий значок на рабочем столе, чтобы запустить диспетчер моментальных снимков StorSimple.

2. На панели **Область** разверните узел **Группы томов**, щелкните правой кнопкой мыши имя группы томов и выберите пункт **Изменить**.

3. Откроется диалоговое окно **Создание группы томов**. Вы можете изменить значения в полях **Имя**, **Приложения** и **Тома**.

4. Нажмите кнопку **ОК**, чтобы сохранить изменения.

## Удаление группы томов

Выполните указанные ниже действия, чтобы удалить группу томов.

>[AZURE.WARNING]Вместе с ней также будут удалены все связанные резервные копии.

#### Удаление группы томов

1. Щелкните соответствующий значок на рабочем столе, чтобы запустить диспетчер моментальных снимков StorSimple. 

2. На панели **Область** разверните узел **Группы томов**, щелкните правой кнопкой мыши имя группы томов и выберите **Удалить**.

3. Откроется диалоговое окно **Удаление группы томов**. Введите **Confirm** (Подтверждаю) в текстовое поле и нажмите кнопку **ОК**.

    Удаленная группа томов исчезнет из списка на панели **Результаты**, а все резервные копии, связанные с этой группой, будут удалены из каталога архивов.

## Дальнейшие действия

- Узнайте, как [использовать диспетчер моментальных снимков StorSimple для администрирования решения StorSimple](storsimple-snapshot-manager-admin.md).
- Узнайте, как [создавать политики архивации и управлять ими с помощью диспетчера моментальных снимков StorSimple](storsimple-snapshot-manager-manage-backup-policies.md).

<!---HONumber=AcomDC_1203_2015-->