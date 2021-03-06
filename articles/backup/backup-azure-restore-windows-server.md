<properties
   pageTitle="Восстановление данных на Windows Server или Windows Client из Azure | Microsoft Azure"
   description="Узнайте, как выполнять восстановление данных с Windows Server или Windows Client."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/25/2016"
	 ms.author="trinadhk; jimpark; markgal;"/>

# Восстановление файлов на компьютер Windows Server или Windows Client
В этой статье описываются действия, необходимые для операций восстановления двух типов:

- Восстановление данных на тот же компьютер, с которого создавались резервные копии.
- Восстановление данных на любой другой компьютер.

В обоих случаях данные извлекаются из хранилища службы архивации Azure.

## Восстановление данных на тот же компьютер
Если вы случайно удалили файл и хотите восстановить его на том же компьютере (с которого создана резервная копия), указанные ниже действия помогут вам восстановить данные.

1. Откройте оснастку **Служба архивации Microsoft Azure**.
2. Щелкните **Восстановить данные**, чтобы запустить рабочий процесс.

    ![Восстановление данных](./media/backup-azure-restore-windows-server/recover.png)

3. Выберите параметр **Этот сервер (*имя\_компьютера*)**, чтобы восстановить файл из резервной копии на том же компьютере.

    ![Тот же компьютер](./media/backup-azure-restore-windows-server/samemachine.png)

4. Выберите **Обзор файлов** или **Поиск файлов**.

    Если вы планируете восстановить один или несколько файлов с известным путем, оставьте вариант по умолчанию. Если вы не знаете структуру папок и хотите найти файл, выберите вариант **Поиск файлов**. В этом разделе мы будем использовать параметр по умолчанию.

    ![Обзор файлов](./media/backup-azure-restore-windows-server/browseandsearch.png)

5. Выберите том, в котором хранится резервная копия файла.

    Можно восстановить файл на любой момент времени. Даты, которые в календаре выделены **полужирным**, означают доступность точки восстановления. После выбора даты в расписании резервного копирования (которое было завершено успешно) в раскрывающемся списке **Время** можно выбрать момент времени.

    ![Том и дата](./media/backup-azure-restore-windows-server/volanddate.png)

6. Выберите элементы, которые следует восстановить. Для восстановления можно выбрать сразу несколько папок и файлов.

    ![Выбор файлов](./media/backup-azure-restore-windows-server/selectfiles.png)

7. Укажите параметры восстановления.

    ![Варианты восстановления](./media/backup-azure-restore-windows-server/recoveroptions.png)

  - Данные можно восстановить в исходное расположение (в котором папка или файл будут перезаписаны) или в другое расположение на том же компьютере.
  - Если восстанавливаемый файл (или папка) существует в целевом расположении, вы можете создать копию (будет две версии одного файла) или перезаписать файлы в целевом расположении. Также можно пропустить восстановление файлов, которые существуют в целевом расположении.
  - Настоятельно рекомендуем оставить параметр восстановление ACL для восстанавливаемых файлов (выбран по умолчанию).

8. Предоставив эти входные данные, нажмите кнопку **Далее**. Начнется рабочий процесс восстановления, который восстанавливает файлы на этом компьютере.

## Восстановление файлов на другой компьютер
При потере всего содержимого сервера вы все равно можете восстановить данные из службы архивации Azure на другом компьютере. Соответствующий рабочий процесс описан ниже.

Ниже приведена терминология, используемая в этих действиях:

- *Исходный компьютер* — компьютер, для данных на котором была создана резервная копия и который в настоящий момент недоступен.
- *Целевой компьютер* — компьютер, на который восстанавливаются данные.
- *Образец хранилища* — хранилище службы архивации, в котором зарегистрированы *исходный* и *целевой* компьютеры. <br/>

> [AZURE.NOTE] Резервные копии, созданные с компьютера с более поздней версией ОС, невозможно восстановить на компьютер с более ранней версией ОС. Например, если резервные копии созданы с компьютера под управлением Windows 7, их можно восстановить на компьютер под управлением Windows 8 или более поздних версий ОС, но не наоборот.

1. Откройте на *целевом компьютере* оснастку **Служба архивации Microsoft Azure**.
2. Убедитесь, что и *целевой*, и *исходный компьютер* зарегистрированы в одном хранилище службы архивации.
3. Щелкните **Восстановить данные**, чтобы запустить рабочий процесс.

    ![Восстановление данных](./media/backup-azure-restore-windows-server/recover.png)

4. Выберите **Другой сервер**.

    ![Другой сервер](./media/backup-azure-restore-windows-server/anotherserver.png)

5. Укажите файл с учетными данными хранилища, который соответствует *примеру хранилища*. Если файл с учетными данными хранилища недействителен (или просрочен), скачайте новый файл из *примера хранилища* на портале Azure. Когда файл с учетными данными будет указан, отобразится соответствующее хранилище службы архивации.

6. Выберите *исходный компьютер* из списка отображенных компьютеров.

    ![Список компьютеров](./media/backup-azure-restore-windows-server/machinelist.png)

7. Выберите **Поиск файлов** или **Обзор файлов**. В этом разделе мы будем использовать **Поиск файлов**.

    ![Поиск](./media/backup-azure-restore-windows-server/search.png)

8. На следующем экране выберите том и дату. Найдите имя папки или файла, который нужно восстановить.

    ![Поиск элементов](./media/backup-azure-restore-windows-server/searchitems.png)

9. Выберите расположение, в которое необходимо восстановить файлы.

    ![Расположение для восстанавливаемых данных](./media/backup-azure-restore-windows-server/restorelocation.png)

10. Укажите парольную фразу для шифрования, заданную во время регистрации *исходного компьютера* в *примере хранилища*.

    ![Шифрование](./media/backup-azure-restore-windows-server/encryption.png)

11. После ввода данных щелкните **Восстановить**, после чего начнется восстановление файлов из резервных копий в указанное расположение.

## Дальнейшие действия
- [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)
- Посетите [форум о службе архивации Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## Подробнее
- [Обзор службы архивации Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425)
- [Резервное копирование виртуальных машин Azure](backup-azure-vms-introduction.md)
- [Резервное копирование рабочих нагрузок Майкрософт](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0128_2016-->