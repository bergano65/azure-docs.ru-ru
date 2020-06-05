---
title: Устранение STOP-ошибки Windows — ошибка при инициализации службы каталогов
description: Устраните проблемы, когда виртуальная машина контроллера домена Active Directory в Azure зависает в цикле, указывая, что требуется перезапуск.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663942"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Устранение STOP-ошибки Windows — ошибка при инициализации службы каталогов

Эта статья содержит инструкции по устранению проблем, когда виртуальная машина контроллера домена Active Directory в Azure зависает в цикле, указывая, что требуется перезапуск.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины на снимке экрана показано, что виртуальная машина должна быть перезапущена из-за ошибки, и отображается код завершения **0xC00002E1** в Windows Server 2008 R2 или **0xC00002E2** в Windows Server 2012 или более поздней версии.

![На заставке Windows Server 2012 выводится сообщение "На вашем ПК возникла проблема, и его необходимо перезагрузить. Мы лишь собираем некоторые сведения об ошибке, а затем выполним перезагрузку".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Причина

Код ошибки **0xC00002E2** представляет **STATUS_DS_INIT_FAILURE**, а код ошибки **0xC00002E1** представляет **STATUS_DS_CANT_START**. Обе ошибки возникают при наличии проблемы со службой каталогов.

По мере загрузки ОС она принудительно перезапускается в автоматическом режиме локальным сервером проверки подлинности для обеспечения безопасности (**LSASS.exe**), который выполняет проверку подлинности входов пользователей. Проверка подлинности не может быть выполнена, если операционная система на виртуальной машине является контроллером домена, у которого нет доступа на чтение и запись к своей локальной базе данных Active Directory. Из-за отсутствия доступа к **Active Directory (AD)** LSASS.exe не может пройти проверку подлинности и принудительно перезапускает ОС.

Эта ошибка может быть вызвана любой из следующих причин:

- Отсутствует доступ к диску, на котором находится локальная база данных AD (**NTDS.DIT**).
- На диске с локальной базой данных AD (NTDS.DIT) закончилось свободное место.
- Отсутствует файл локальной базы данных AD (NTDS.DIT).
- Виртуальная машина имеет несколько дисков, а политика сети хранения данных (SAN) настроена неправильно. Политика SAN не настроена как **ONLINEALL**, а диски, не относящиеся к ОС, подключены в диспетчере дисков в автономном режиме.
- Файл локальной базы данных AD (NTDS.DIT) поврежден.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе:

1. Создайте виртуальную машину восстановления и получите к ней доступ.
1. Освободите место на диске.
1. Убедитесь, что диск, содержащий базу данных AD, подключен.
1. Включите режим восстановления служб каталогов.
1. **Рекомендуется**. Перед перестроением виртуальной машины включите серийную консоль и сбор дампов памяти.
1. Перестройте виртуальную машину.
1. Перенастройте политику SAN.

> [!NOTE]
> При возникновении этой ошибки гостевая ОС не работает. Для устранения этой проблемы вы будете устранять неполадки в автономном режиме.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и получение доступа к ней

1. Выполните [шаги 1–3 списка команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example), чтобы подготовить виртуальную машину для восстановления.
1. Используйте подключение к удаленному рабочему столу, чтобы подключиться к виртуальной машине для восстановления.

### <a name="free-up-space-on-disk"></a>Освобождение места на диске

Так как диск теперь подключен к виртуальной машине для восстановления, убедитесь, что диск, на котором находится внутренняя база данных Active Directory, имеет достаточно места для правильной работы.

1. Проверьте, не заполнен ли диск, щелкнув диск правой кнопкой мыши и выбрав **Свойства**.
1. Если на диске менее 300 МБ свободного места, [увеличьте его до 1 ТБ с помощью PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk).
1. Если на занятое пространство на диске достигло 1 ТБ, выполните очистку диска.

   1. Используйте PowerShell, чтобы [отключить диск данных](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell) от поврежденной виртуальной машины.
   1. После отключения от поврежденной виртуальной машины [подключите диск данных](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm) к работающей виртуальной машине.
   1. Освободите дополнительное место с помощью [средства очистки диска](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup).

1. **Необязательно**. Если требуется больше места, откройте экземпляр CMD и введите команду `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g`, чтобы выполнить дефрагментацию диска:

  * В команде замените `<LETTER ASSIGNED TO THE OS DISK>` на букву диска ОС. Например, если используется буква диска `F:`, команда будет выглядеть как `defrag F: /u /x /g`.

  * В зависимости от уровня фрагментации дефрагментация может занять несколько часов.

Если на диске достаточно места, перейдите к следующей задаче.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Убедитесь, что диск, содержащий базу данных Active Directory, подключен.

1. Откройте экземпляр CMD с повышенными привилегиями и введите следующие команды:

   1. Загрузите файл реестра:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      Назначение `f:` предполагает, что диск имеет букву `F:`. Используйте букву диска, заданную для диска ОС.

   1. Определите букву диска и папку для **NTDS.DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Выгрузите файл реестра:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. С помощью портала Azure убедитесь, что диск, где настроен NTDS.DIT, добавлен на виртуальную машину.
1. С помощью консоли управления дисками в гостевой ОС убедитесь, что диск с NTDS.DIT подключен к сети.
   1. Средство управления дисками можно найти в разделе **Администрирование > Управление компьютером > Хранилище**, также доступ к нему можно получить с помощью команды `diskmgmt.msc` в экземпляре CMD.
1. Если диск не подключен к виртуальной машине, подключите его повторно, чтобы устранить проблему.

   Если диск был подключен обычным образом, перейдите к следующей задаче.

### <a name="enable-directory-services-restore-mode"></a>Включение режима восстановления служб каталогов

Настройте виртуальную машину для загрузки в режиме **восстановления служб каталогов (DSRM)** , чтобы обойти проверку существования файла NTDS.DIT во время загрузки.

1. Прежде чем продолжить, убедитесь, что вы выполнили предыдущие задачи для подключения диска к виртуальной машине восстановления, а также определили, на каком диске находится файл NTDS.DIT.
1. Используя экземпляр CMD с повышенными привилегиями, выведите сведения о загрузочном разделе в этом хранилище, чтобы найти идентификатор из активного раздела:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Замените `< Drive Letter >` буквой, определенной на предыдущих шагах.

   ![На снимке экрана показан экземпляр CMD с повышенными привилегиями после ввода команды "bcdedit /store <буква диска>:\boot\bcd /enum", которая отображает диспетчер загрузки Windows с идентификатором.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Включите флаг `safeboot DsRepair` для загрузочного раздела:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Замените `< Drive Letter >` и `< Identifier >` значениями, определенными в предыдущих шагах.

1. Снова запросите параметры загрузки, чтобы убедиться, что изменение внесено надлежащим образом.

   ![На снимке экрана показан экземпляр CMD с повышенными привилегиями после включения флага safeboot DsRepair.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Рекомендация: перед перестроением виртуальной машины включите серийную консоль и сбор дампов памяти

Чтобы включить сбор дампов памяти и серийную консоль, запустите приведенный ниже сценарий, открыв сеанс командной строки с повышенными привилегиями (запуск от имени администратора) и выполните приведенные ниже команды.

1. Включите серийную консоль:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Убедитесь, что свободное место на диске ОС не меньше объема памяти (ОЗУ) на виртуальной машине.

  1. Если места на диске ОС недостаточно, измените расположение создания файла дампа памяти и укажите любой к виртуальной машине диск данных, на котором достаточно свободного места.

     Чтобы изменить расположение, замените `%SystemRoot%` на букву (например, `F:`) диска данных в приведенных ниже командах.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Для включения дампа ОС предлагается следующая конфигурация:

  **Загрузка неработающего диска ОС**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Включение в ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Включение в ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Выгрузка неработающего диска ОС**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Перестроение виртуальной машины

1. Чтобы заново собрать виртуальную машину, выполните [шаг 5 из списка команд для восстановления ВМ](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example).

### <a name="reconfigure-the-storage-area-network-policy"></a>Перенастройка политики сети хранения данных

1. При загрузке в режиме DSRM единственным пользователем, доступным для входа, является администратор восстановления, который использовался при повышении уровня виртуальной машины до контроллера домена. Для всех остальных пользователей будет выводиться ошибка проверки подлинности.

   1. Если никакого другого контроллера домена нет, необходимо войти в систему локально, используя `.\administrator` или `machinename\administrator` и пароль DSRM.

1. Настройте политику SAN так, чтобы все диски были подключены к сети.

   1. Откройте экземпляр CMD с повышенными привилегиями и введите `DISKPART`.
   1. Запросите список дисков.

      `DISKPART> list disk`

   1. Введите следующие команды, чтобы выбрать диск, который необходимо подключить к сети, и изменить политику SAN:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. После устранения проблемы убедитесь, что флаг `DsRepair safeboot` удален:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Перезапустите виртуальную машину.

   > [!NOTE]
   > Если виртуальная машина была только что перенесена из локальной среде в Azure и вы хотите перенести аналогичным образом дополнительные контроллеры домена, рекомендуется следовать инструкциям из следующей статьи, чтобы предотвратить возникновение этой проблемы при будущих миграциях.
   >
   > [Передача существующих локальных контроллеров домена Hyper-V в Azure с помощью Azure PowerShell](https://support.microsoft.com/help/2904015)
