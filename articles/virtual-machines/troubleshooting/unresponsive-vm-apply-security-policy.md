---
title: Виртуальная машина Azure не отвечает при применении политики безопасности к системе
description: В этой статье приводятся инструкции по устранению проблем, в которых экран загрузки зависает, когда виртуальная машина не отвечает при применении политики безопасности к системе на виртуальной машине Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908065"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Виртуальная машина Azure не отвечает при применении политики безопасности к системе

В этой статье приводятся инструкции по устранению проблем, при которых операционная система перестает отвечать на запросы, пока применяется политика безопасности на виртуальной машине Azure.

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается ОС, которая была заблокирована при загрузке с сообщением:

> "Применение политики безопасности к системе".

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Снимок экрана: экран запуска Windows Server 2012 R2 зависает.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Снимок экрана: экран запуска Windows Server 2012 R2 зависает.":::

## <a name="cause"></a>Причина

Существует множество потенциальных причин этой проблемы. Вы не сможете получить сведения об источнике, пока не будет выполнен анализ дампа памяти.

## <a name="resolution"></a>Решение

### <a name="process-overview"></a>Обзор процесса

1. [Создание виртуальной машины для восстановления и доступ к ней](#create-and-access-a-repair-vm)
2. [Включение сбора последовательной консоли и дампа памяти](#enable-serial-console-and-memory-dump-collection)
3. [Перестроение виртуальной машины](#rebuild-the-vm)
4. [Получение файла дампа памяти](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Создание виртуальной машины для восстановления и получение доступа к ней

1. Выполните [шаги 1–3 списка команд для восстановления виртуальной машины](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example), чтобы подготовить виртуальную машину для восстановления.
2. Используйте подключение к удаленному рабочему столу подключиться к виртуальной машине восстановления.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Включение сбора последовательной консоли и дампа памяти

Чтобы включить сбор резервных копий в памяти и последовательную консоль, выполните следующий скрипт:

1. Откройте сеанс командной строки с повышенными привилегиями (запуск от имени администратора).
2. Перечислите данные из хранилища BCD и определите идентификатор загрузчика, который будет использоваться на следующем шаге.

     1. Для виртуальной машины поколения 1 введите следующую команду и запишите идентификатор в списке:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        В команде замените на \<BOOT PARTITON> букву раздела на подключенном диске, который содержит папку Boot.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Снимок экрана: экран запуска Windows Server 2012 R2 зависает." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Выгрузите поврежденный диск ОС.

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Перестроение виртуальной машины

Чтобы заново собрать виртуальную машину, выполните [шаг 5 из списка команд для восстановления ВМ](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example).

### <a name="collect-the-memory-dump-file"></a>Получение файла дампа памяти

Чтобы устранить эту проблему, необходимо сначала собрать файл дампа памяти для аварийного восстановления и обратиться в службу поддержки с файлом дампа памяти. Чтобы получить файл дампа, выполните следующие действия.

1. Подключите диск операционной системы к новой виртуальной машине восстановления.

    - Выполните [шаги 1-3 команды восстановления виртуальной машины](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , чтобы ПОДГОТОВИТЬ новую виртуальную машину для восстановления.
    - Используйте подключение к удаленному рабочему столу подключиться к виртуальной машине восстановления.

2. Найдите файл дампа и отправьте запрос в службу поддержки:

    - На виртуальной машине восстановления перейдите в папку Windows на подключенном диске ОС. Если подключенному диску ОС присвоена буква `F`, то необходимо перейти в `F:\Windows`.
    - Найдите файл Memory. dmp и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа памяти.
    - Если при поиске файла Memory. dmp возникают проблемы, можно использовать [вызовы прерываний (NMI) в последовательной консоли](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Вы можете следовать указаниям по [созданию файла аварийного дампа с помощью вызовов NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Дальнейшие действия

При возникновении проблем, связанных с применением политик локальных пользователей и групп, см. раздел [VM не отвечает при применении политики локальных пользователей и групп групповая политика](unresponsive-vm-apply-group-policy.md)