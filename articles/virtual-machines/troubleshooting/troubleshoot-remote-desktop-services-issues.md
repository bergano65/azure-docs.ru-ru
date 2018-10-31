---
title: Службы удаленных рабочих столов не запускаются на виртуальной машине Azure | Документация Майкрософт
description: Узнайте, как устранять неполадки служб удаленных рабочих столов при подключении к виртуальной машине.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989020"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Службы удаленных рабочих столов не запускаются на виртуальной машине Azure

В этой статье описывается устранение неполадок подключения к виртуальной машине Azure, когда службы удаленных рабочих столов (TermService) не запускаются или их запуск завершается ошибкой.

>[!NOTE]
>В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания Resource Manager. Для новых развертываний рекомендуется использовать эту модель развертывания вместо классической.

## <a name="symptoms"></a>Проблемы

При попытке подключиться к виртуальной машине возникают следующие сценарии:

- Снимок экрана виртуальной машины: операционная система полностью загружена и ожидает учетные данные.
- Все приложения на виртуальной машине работают правильно и доступны.
- Виртуальная машина отвечает на подключения TCP через RDP-порт (по умолчанию 3389).
- Вы не получаете запрос учетных данных при попытке подключения по протоколу удаленного рабочего стола.

## <a name="cause"></a>Причина:

Эта проблема возникает, поскольку службы удаленных рабочих столов не выполняются на виртуальной машине. Причина может быть в следующем:

- служба TermService **отключена**;
- произошло аварийное завершение или зависание службы TermService.

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, используйте одно из следующих решений или попробуйте применить их одно за другим.

### <a name="solution-1-using-the-serial-console"></a>Решение 1. Использование последовательной консоли

1. Откройте [последовательную консоль](serial-console-windows.md), выбрав **Поддержка и устранение неисправностей** > **Последовательная консоль (предварительная версия)**. Если эта функция включена на виртуальной машине, вы сможете успешно подключиться к виртуальной машине.

2. Создайте канал для экземпляра командной строки. Введите **CMD**, чтобы запустить канал и получить имя канала.

3. Переключитесь на канал, в котором выполняется экземпляр командной строки, в этом случае следует использовать канал 1.

   ```
   ch -si 1
   ```

4. Нажмите клавишу **ВВОД** еще раз и введите допустимое имя пользователя и пароль (локальный или доменный идентификатор) для виртуальной машины.

5. Запросите состояние службы TermService.

   ```
   sc query TermService
   ```

6. Если отображается состояние службы **остановлена**, попытайтесь запустить службу.

   ```
   sc start TermService
   ```

7. Запросите службу еще раз, чтобы убедиться, что она успешно запущена.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Решение 2. Использование виртуальной машины восстановления для включения службы

[Выполните резервное копирование диска операционной системы](../windows/snapshot-copy-managed-disk.md) и [подключите диск ОС к виртуальной машине восстановления](../windows/troubleshoot-recovery-disks-portal.md). Откройте экземпляр командной строки с повышенными привилегиями и выполните следующие сценарии на виртуальной машине восстановления:

>[!NOTE]
>Мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением на своей виртуальной машине. После этого отключите диск от виртуальной машины восстановления и [воссоздайте свою виртуальную машину](../windows/create-vm-specialized.md). Для дальнейшего устранения неполадок можно использовать **Решение 1**, так как теперь последовательная консоль включена.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
