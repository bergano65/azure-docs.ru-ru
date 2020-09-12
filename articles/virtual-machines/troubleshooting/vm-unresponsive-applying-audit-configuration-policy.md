---
title: При применении политики конфигурации политики аудита виртуальная машина не отвечает.
description: В этой статье приведены инструкции по устранению проблем, при которых виртуальная машина перестает отвечать на запросы при применении политики конфигурации политики аудита, что предотвращает загрузку виртуальной машины Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: bc41783bf977806b5f9bba5b953f1f581ad07f18
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299530"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>При применении политики конфигурации политики аудита виртуальная машина не отвечает.

В этой статье приведены инструкции по устранению проблем, при которых виртуальная машина перестает отвечать на запросы при применении политики конфигурации политики аудита, что предотвращает загрузку виртуальной машины Azure.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается, что операционная система (ОС) не отвечала во время загрузки с сообщением **применение политики конфигурации политики аудита**.

  ![Загрузка ОС с сообщением: "применение политики конфигурации политики аудита"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Загрузка ОС в Windows Server 2012 с сообщением «применение политики конфигурации политики аудита».](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Причина

Существуют конфликтующие блокировки, когда политика пытается очистить старые профили пользователей.

> [!NOTE]
> Это применимо только к Windows Server 2012 и Windows Server 2012 R2.

Ниже приведена проблемная политика: *шаблоны конфигурации компьютера \ Системный/пользователь профилес\делете профили пользователей старше указанного числа дней при перезапуске системы.*

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Общие сведения о процессе

1. Создайте виртуальную машину для восстановления и войдите на нее.
1. Отключение политики.
1. Включите последовательную консоль и сбор дампов памяти.
1. Перестройте виртуальную машину.
1. Собирайте файл дампа памяти и отправьте запрос в службу поддержки.

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и вход на нее

1. Выполните шаги 1–3 из списка [команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands), чтобы подготовить виртуальную машину для восстановления.
1. Используйте подключение к удаленному рабочему столу, чтобы подключиться к виртуальной машине для восстановления.

### <a name="disable-the-policy"></a>Отключение политики

1. На виртуальной машине восстановления откройте **редактор реестра**.
1. Перейдите к разделу **HKEY_LOCAL_MACHINE** и выберите в меню **файл > загрузить Hive** .

   ![Навигация в редакторе реестра для загрузки Hive.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Можно использовать Load Hive для загрузки разделов реестра из автономной системы. В этом случае система является поврежденным диском, подключенным к виртуальной машине восстановления.
   - Параметры на уровне системы хранятся на **HKEY_LOCAL_MACHINE** и могут быть сокращены в качестве **HKLM**.

1. Откройте файл на подключенном диске `\windows\system32\config\SOFTWARE` .

   - Когда появится запрос на ввод имени, введите **BROKENSOFTWARE**.
   - Чтобы проверить, загружен ли **BROKENSOFTWARE** , разверните **HKEY_LOCAL_MACHINE** и найдите добавленный ключ **BROKENSOFTWARE** .

1. Перейдите по адресу **BROKENSOFTWARE** и проверьте, существует ли ключ **клеануппрофилес** в загруженном Hive.

   - Если ключ существует, задается политика **клеануппрофилес** . Его значение представляет политику хранения, измеряемую в днях.
   - Если ключ не существует, политика **клеануппрофилес** не устанавливается. В этом случае пропустите, чтобы [Отправить запрос в службу поддержки с файлом дампа памяти](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Удалите ключ **клеануппрофилес** с помощью следующей команды:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Выгрузите куст **BROKENSOFTWARE** с помощью следующей команды:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Включение последовательной консоли и сбор дампов памяти

**Рекомендуется**. Прежде чем перестраивать виртуальную машину, включите последовательную консоль и сбор дампов памяти. Для этого выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями от имени администратора.
1. Перечислите данные из хранилища BCD и определите идентификатор загрузчика, который будет использоваться на следующем шаге.

   1. Для виртуальной машины поколения 1 введите следующую команду и запишите идентификатор в списке:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - В команде замените на `<BOOT PARTITON>` букву раздела на подключенном диске, который содержит папку Boot.

        ![На рис. 4 показаны выходные данные для вывода данных из хранилища BCD в виртуальной машине поколения 1, которая отображается под загрузкой Windows в качестве идентификатора.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. Для виртуальной машины поколения 2 введите следующую команду и запишите идентификатор в списке:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - В команде замените на `<LETTER OF THE EFI SYSTEM PARTITION>` букву системного раздела EFI.
      - Может быть полезно запустить консоль управления дисками, чтобы указать соответствующий системный раздел, помеченный как **системный раздел EFI**.
      - Идентификатор может быть уникальным GUID или **диспетчером загрузки**по умолчанию.

1. Выполните следующие команды:

   **Включите последовательную консоль.**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Убедитесь, что размер свободного места на диске ОС превышает объем памяти (ОЗУ) этой виртуальной машины.

   Если места на диске ОС недостаточно, измените расположение для создания файла дампа памяти, указав подключенный к виртуальной машине диск данных, на котором достаточно свободного места. Чтобы изменить расположение, замените значение **%SystemRoot%** буквой нужного диска данных (например, **F:** ) в приведенных ниже командах.

   Рекомендуемая конфигурация для включения дампа ОС.

   **Загрузите куст реестра с поврежденного диска ОС.**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Включите сбор для ControlSet001.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Включите сбор для ControlSet002.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Выгрузите поврежденный диск ОС.**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-virtual-machine"></a>Перестроение виртуальной машины

1. Чтобы перестроить виртуальную машину, [выполните шаг 5 из списка команд для восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example).

1. Проверьте, не загружается ли в обычном случае виртуальная машина, чтобы узнать, устранена ли проблема.

   - Если проблема не устранена, продолжите [Создание файла дампа и отправьте запрос в службу поддержки](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Если проблема устранена, дальнейшие действия не требуются.

Если проблема была исправлена, политика теперь отключена локально. Для постоянного решения не используйте политику Клеануппрофилес на виртуальных машинах, так как она будет автоматически удалять профили пользователей. Используйте другой метод для выполнения очистки профиля, например запланированной задачи или скрипта.

**Не использовать эту политику:** 
 *Мачине\админ Темплатес\систем\усер профилес\делете User Profiles старше указанного числа дней при перезапуске системы.*

### <a name="the-issue-should-now-be-fixed"></a>Теперь проблема должна быть исправлена

Протестируйте виртуальную машину, чтобы убедиться, что она работает как нормальная. Если у вас по-прежнему возникают проблемы, можно перейти к следующему разделу для получения дополнительной помощи.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Получение файла дампа памяти и отправка запроса в службу поддержки

Чтобы устранить эту проблему, необходимо сначала собрать файл дампа памяти для сбоя, а затем обратиться в службу поддержки с файлом дампа памяти. Чтобы получить файл дампа, выполните следующие действия.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Подключение диска ОС к новой виртуальной машине восстановления

1. Выполните шаги 1-3 [команды восстановления виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , чтобы ПОДГОТОВИТЬ новую виртуальную машину для восстановления.
1. Используйте подключение к удаленному рабочему столу, чтобы подключиться к виртуальной машине для восстановления.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Размещение файла дампа и отправка запроса в службу поддержки

1. На виртуальной машине восстановления перейдите в папку Windows на подключенном диске ОС. Если буква драйвера, назначенная подключенному диску ОС, обозначена как *F*, необходимо обратиться к `F:\Windows` .
1. Найдите `memory.dmp` файл и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа памяти.
1. Если у вас возникли проблемы с нахождением `memory.dmp` файла, используйте вместо него [вызовы прерываний (NMI) в последовательной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Следуйте указаниям по [созданию файла аварийного дампа с помощью вызовов NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).
