---
title: Виртуальная машина не отвечает при применении политики «Групповая политика местных пользователей & групп»
description: В этой статье приводятся шаги для решения проблем, в которых экран загрузки застрял при применении политики во время загрузки в виртуальной машине Azure (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620861"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Виртуальная машина не отвечает при применении политики «Групповая политика местных пользователей & групп»

В этой статье приводятся шаги для решения проблем, в которых экран загрузки застрял при применении политики во время загрузки в виртуальной машине Azure (VM).

## <a name="symptom"></a>Симптом

При [использовании диагностики Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) для просмотра скриншота VM, вы увидите, что экран застрял загрузки с сообщением: *Применение политики группы местных пользователей и групп политики.*

![Alt text: Экран, показывающий загрузку политики «Применяя групповую политику» (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alt text: Экран, показывающий загрузку политики «Применяя групповую политику» (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Причина

Симптомы этого замораживания вызваны дефектом кода в библиотеке динамической ссылки службы профиля Windows *(profsvc.dll*).

> [!NOTE]
> Этот дефект применяется только на Windows Server 2012 и Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Политика, о котором идет речь

Применяемая политика, которая не завершит процессы:

* *Компьютерная конфигурация,Политика -Административные шаблоны/Система/Профили пользователей, удалить профили пользователей старше указанного количества дней при перезагрузке системы*

Эта политика будет висеть только в том случае, если следующие шесть условий верны:

* *Профили пользователя Delete старше определенного количества дней в политике перезагрузки системы* включены.
* У вас есть профили, отвечающие возрастные требования, требующие очистки.
* У вас есть компоненты, которые зарегистрировались для уведомления об удалении профилей.
* Компоненты делают звонки (прямые или косвенные), которые должны получить данные из компонентов управления службой (SCM) Windows, такие как Start, Stop или Информация о запросе об службе.
* У вас есть служба настроена, чтобы начать как *автоматический*.
* Эта услуга будет работать в контексте учетной записи домена (в отличие от использования встроенной учетной записи, например локальной системы).

### <a name="the-code-defect"></a>Дефект кода

Дефект кода связан с тем, что диспетчер службы (SCM) и службы профиля пытаются применять блокировки друг на друга одновременно. Блокировки существуют для предотвращения внесения нескольких служб в одни и те же данные одновременно, что может привести к коррупции. Обычно несколько запросов на блокировку не вызывают проблемы. Однако, поскольку это происходит во время загрузки, ни одна из служб не может завершить свои процессы, так как они застряли в ожидании друг друга.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - Диспетчер службы блокирует политику "Удалить профили пользователей при перезагрузке"

Есть два действия, которые перекрываются так, что:

* Action 1 приобретает блокировку профиля, но еще не приобрел блокировку SCM.

  **И**

* Action 2 приобретает блокировку SCM, но еще не приобрел блокировку профиля.

Как только этот тупик происходит, попытка приобрести второй необходимый замок зависает действие.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Действие 1 - Уведомление об удалении старого профиля (имеет **блокировку профиля,** нуждается в **блокировке SCM)**

1. Во-первых, набор политики для удаления старых профилей приобретает блокировку службы внутреннего профиля.

   * Эта блокировка предназначена для предотвращения взаимодействия двух потоков с профилями во время *выполнения операции удаления.*

2. Политика находит профили, которые достаточно стары для удаления.
3. В рамках удаления профиля компонент, зарегистрированный для уведомлений об удалении профиля, пытается **запустить службу.**
4. Перед запуском службы диспетчеру службы (SCM) необходимо приобрести **внутренний замок SCM,** удерживаемый потоками в **действии 2.**

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Действие 2 - Загрузка профиля/создание для конкретных данных пользователя (имеет **блокировку SCM,** нуждается **в блокировке профиля)**

1. При загрузке SCM необходимо заказать все службы *автоматического запуска* своей группой, а также любые службы, от которые зависят эти службы.

2. **SCM приобретает внутренний замок SCM, используемый** для управления доступом к службам запуска, остановки или настройки при заказе служб.

3. Как только службы в порядке, SCM петли через каждую службу и запускает его.

4. Если служба работает в контексте учетной записи домена, профиль должен быть загружен или создан для учетной записи домена, чтобы он мог хранить определенные данные пользователя.

5. Этот запрос отправляется в **Профильную службу.**

6. Служба профиля нуждается в доступе к **внутренней блокировке,** приобретенной в **действии 1**.

## <a name="solution"></a>Решение

### <a name="process-overview"></a>Обзор процесса

1. Создание и доступ к ремонту VM
2. Включить серийную консоль и коллекцию памяти
3. Восстановить VM
4. Сбор файла свалки памяти

   > [!NOTE]
   > При возникновении этой ошибки загрузки ОС guest не работает. Вы будете устранения неполадок в автономном режиме, чтобы решить эту проблему.

### <a name="create-and-access-a-repair-vm"></a>Создание и доступ к ремонту VM

1. Используйте [шаги 1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) для подготовки ремонтного VM.
2. Использование удаленного соединения рабочего стола подключается к Ремонту VM.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Включить серийную консоль и коллекцию памяти

Для включения сбора пультов памяти и последовательной консоли выполнить сценарий ниже:

1. Откройте сеанс повышенной команды (запуск в качестве администратора).
2. Выполните следующие команды:

   * Включить серийную консоль:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Убедитесь, что свободное пространство на диске ОС столько, сколько размер памяти (RAM) на VM.

   * Если на диске ОС недостаточно места, следует изменить место, где будет создан файл свалки памяти, и сослаться на то, что на любой диск данных, прикрепленный к VM, который имеет достаточно свободного пространства. Чтобы изменить местоположение, `%SystemRoot%` замените дисковую букву (например, "F:") диска данных в нижеприведенных командах.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Предлагаемая конфигурация для включения Дампа ОС

**Нагрузка Сломанный диск ОС:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Включить ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Включить ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Восстановить VM

Используйте [шаг 5 команды по ремонту VM,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) чтобы собрать VM.

### <a name="collect-the-memory-dump-file"></a>Сбор файла свалки памяти

Чтобы решить эту проблему, сначала необходимо собрать файл сбоя памяти и связаться с поддержкой с файлом свалки памяти. Чтобы собрать файл дампа, выполните следующие действия:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Прикрепите диск ОС к новому Repair VM

1. Используйте шаги [1-3 команды по ремонту VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) для подготовки нового Repair VM.

2. Использование удаленного соединения рабочего стола подключается к Ремонту VM.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Найдите файл свалки и отправьте билет поддержки

1. На ремонте VM, перейдите к папке окна в прикрепленном диске ОС. Если подключенному диску ОС присвоена буква F, то необходимо перейти в F:\Windows.

2. Найдите файл memory.dmp, а затем [отправьте билет поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом свалки памяти.

3. Если у вас возникли проблемы с поиском файла memory.dmp, вы можете использовать [не маскируемые прерывания (NMI) звонки в серийной консоли](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) вместо. Вы можете следовать руководству для [создания ядра или полного](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) файла аварийного сбоя с помощью вызовов NMI.
