---
title: Устранение неполадок при восстановлении файлов виртуальной машины Azure
description: Устранение неполадок, связанных с восстановлением файлов и папок из резервной копии виртуальной машины Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513411"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Устранение неполадок при восстановлении файлов резервной копии виртуальных машин Azure

В этой статье содержатся инструкции по устранению неполадок, связанных с Azure Backupми ошибками, относящимися к восстановлению файлов и папок из резервной копии виртуальной машины Azure. 

## <a name="exception-caught-while-connecting-to-target"></a>Перехвачено исключение при подключении к целевому объекту

Возможная причина: скрипт не может получить доступ к рекомендуемому действию точки восстановления: Проверьте, соответствует ли компьютер всем [требованиям доступа](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Целевой объект уже вошел в систему через сеанс iSCSI

Возможная причина: сценарий уже был выполнен на том же компьютере, а диски подключены.
Рекомендуемое действие: тома точки восстановления уже подключены. Они могут быть не подключены к одной и той же букве диска исходной виртуальной машины. Просмотрите все доступные тома в проводнике.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>This script is invalid because the disks have been dismounted via portal/exceeded the 12-hr limit. Скачать новый скрипт с портала

Возможная причина: диски были отключены от портала или превышено 12-часовое ограничение.
Рекомендуемое действие: Скрипт недействителен через 12 часов с момента его скачивания и не может быть выполнен. Посетите портал и Скачайте новый скрипт, чтобы продолжить восстановление файлов.

## <a name="troubleshooting-common-issues"></a>Устранение распространенных проблем

В этом разделе содержатся инструкции по устранению неполадок, возникших при скачивании и выполнении скрипта для восстановления файлов.

## <a name="cannot-download-the-script"></a>Не удается скачать скрипт

Рекомендуемое действие:

1. Убедитесь, что у вас есть все [необходимые разрешения для скачивания скрипта](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Убедитесь в наличии подключения к целевым IP-адресам Azure.
Чтобы проверить подключение, выполните следующую команду из командной строки с повышенными привилегиями. 
    - *nslookup download.Microsoft.com* или
    - *Проверка связи download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Сценарий успешно скачивается, но не запускается

### <a name="for-sles-12-sp4-os-linux"></a>Для ОС SLES 12 SP4 (Linux)

Ошибка: iscsi_tcp_module не найден

Возможная причина: при выполнении скрипта Python для восстановления на уровне элементов в SUSE Linux OS Version 12sp4 происходит сбой с ошибкой ***iscsi_tcp не удается загрузить модуль** _. Модуль ILR использует "iscsi_tcp" для установления TCP-подключения к службе резервного копирования, SUSE как часть 12SP4 Release удалил iscsi_tcp из пакета Open-iSCSI, поэтому операция ILR завершается ошибкой.

Рекомендуемое действие: выполнение скрипта восстановления файлов не поддерживается на виртуальных машинах SUSE 12SP4. Попробуйте выполнить операцию восстановления в более старой версии SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Скрипт выполняется, но подключение к целевому объекту iSCSI завершилось сбоем

Ошибка: при подключении к целевому объекту перехвачено исключение

1. Убедитесь, что компьютер, на котором выполняется сценарий, соответствует всем [требованиям доступа](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Проверьте подключение к целевым IP-адресам Azure.
Чтобы проверить подключение, выполните следующую команду из командной строки с повышенными привилегиями. 
    - _nslookup download.microsoft.com * или<br>
    - *Проверка связи download.microsoft.com*
3. Убедитесь в наличии доступа к исходящему порту iSCSI 3260.
4. Убедитесь, что отсутствуют брандмауэры и NSG, блокирующие трафик в целевые IP-адреса Azure или URL-адрес службы восстановления.
5. Убедитесь, что антивирусная программа блокирует выполнение скрипта.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Подключено к точке восстановления, но диски не подключены

Убедитесь, что у вас есть [правильный компьютер для выполнения сценария](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) .

### <a name="on-windows-vm"></a>На виртуальной машине Windows

**Пул носителей на виртуальной машине подключен в режиме только для чтения** Для Windows 2012 R2 и Windows 2016 (с пулом носителей) при первом запуске сценария пул носителей, подключенный к виртуальной машине, может переключиться в состояние "только для чтения".

Чтобы устранить эту проблему, необходимо вручную задать Read-Write доступ к пулу носителей в первый раз и подключить виртуальные диски, выполнив следующие действия:

1. Задайте Read-Write доступа.
Перейдите в раздел диспетчер сервера > Files and Storage Services > Volumes > Storage Pools.

   ![Хранилище Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. В окне **пул носителей** щелкните правой кнопкой мыши доступный пул носителей и выберите пункт **задать Read-Write доступа** .

   ![Чтение и запись в хранилище Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. После назначения пула носителей с доступом на чтение и запись необходимо подключить виртуальный диск.
Перейдите к **Диспетчер сервера пользовательскому интерфейсу** в разделе виртуальные диски > щелкните правой кнопкой мыши, чтобы выбрать параметр **присоединить виртуальный диск** .

   ![Виртуальный диск диспетчера сервера](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>На виртуальной машине Linux

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Восстановлению файла не удается выполнить автоматическое подключение, так как диск не содержит томов

При восстановлении файлов служба архивации обнаруживает тома и автоматически подключает их. Однако если резервные диски содержат необработанные разделы, эти диски не будут подключены к автоматической установке, и диск данных невозможно будет увидеть для восстановления.

Чтобы решить эту проблему, выполните действия, описанные в этой [статье](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>ОС не удалось опознать файловую систему, вызывающую сбой восстановления файлов Linux при подключении дисков

При запуске диска данных сценария восстановления файлов не удалось подключиться со следующей ошибкой: *не удалось подключить следующие секции, так как ОС не удалось определить файловую систему*

- Чтобы устранить эту проблему, проверьте, зашифрован ли том с помощью приложения стороннего производителя. При шифровании диск или виртуальная машина не будут отображаться на портале как зашифрованные.
1. Войдите на виртуальную машину с резервным копированием и выполните команду: *лсблк-f* .<br>

   ![Диск без тома](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Проверьте файловую систему и шифрование.
3. Если том зашифрован, восстановление файлов не поддерживается. [Подробнее.](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Диски подключены, но не удается подключить тома

- Убедитесь, что у вас есть [правильный компьютер для выполнения скрипта](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>На виртуальной машине Windows

При выполнении скрипта восстановления файлов для Windows появляется сообщение о том, что к файлам (***0) подключено** значение _, но диски обнаруживаются в консоли управления дисками. При подключении томов через iSCSI некоторые обнаруженные тома переходят в автономный режим. Когда канал iSCSI обменивается данными между виртуальной машиной и службой, он обнаруживает эти тома и переводит их в режим «в сети», но они не подключены.

   ![Диск не подключен](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Чтобы определить и устранить эту проблему, выполните следующие действия.

1. Перейдите в раздел _ *Disk Management**, выполнив команду **diskmgmt** в окне cmd.
2. Проверьте, отображаются ли дополнительные диски. В приведенном ниже примере диск 2 является дополнительным диском.

   ![Диск management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Щелкните правой кнопкой мыши **том** и выберите команду **изменить букву диска и пути**.

   ![Диск management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. В окне **добавить букву диска или путь** выберите **назначить следующую букву диска** и назначить доступный диск и нажмите кнопку **ОК**. 

   ![Диск management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. В проводнике можно просмотреть диск.

   ![Диск management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Новые тома должны быть присоединены.  

   ![Диск не подключен](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. В проводнике новые тома будут видны после выполнения скрипта.

   ![Диск не mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Диск не mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>На виртуальных машинах Linux 

- Убедитесь, что у вас есть [правильный компьютер для выполнения скрипта](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Если защищенная виртуальная машина Linux использует массивы LVM и/или RAID, выполните действия, описанные в этой [статье](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Не удается скопировать файлы из подключенных томов

Копирование может завершиться ошибкой с ошибкой 0x80070780: файл недоступен системе. В этом случае проверьте, включена ли Дедупликация дисков на исходном сервере. Затем убедитесь, что на дисках также включено Дедупликация на сервере восстановления. Вы можете оставить роль дедупликации ненастроенной, чтобы не дублировать диски на сервере восстановления.
