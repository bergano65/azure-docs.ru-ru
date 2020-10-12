---
title: Обновление виртуальной машины Azure с SUSE Linux Enterprise Server до SUSE 15 с пакетом обновления 1 (SP1) | Документация Майкрософт
description: Эта статья содержит общие инструкции по использованию системы миграции дистрибутива SUSE для обновления SUSE Linux Enterprise Server до SUSE 15 с пакетом обновления 1 (SP1) для виртуальной машины Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359517"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Обновление виртуальной машины Azure с SLES 12 до SLES 15 с пакетом обновления 1 (SP1)

Эта статья содержит общие инструкции по обновлению SUSE Linux Enterprise Server (SLES) 12 до SLES 15 с пакетом обновления 1 (SP1) для виртуальной машины Azure. Дополнительные сведения см. в статье об [использовании системы переноса дистрибутива SUSE](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) и разделе руководства [по обновлению до SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Поддерживаемые варианты обновления
Чтобы перейти на использование SLES 15 SP1, текущая версия SLES должна быть SLES 12 SP4 или 12 SP5

![Снимок экрана поддерживаемых вариантов обновления](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Предварительные требования

- Планируйте перенос согласно утвержденному окну простоя. Это связано с тем, что виртуальная машина перезагружается во время переноса.
- Перед переносом создайте полную резервную копию виртуальной машины.
- Если резервное копирование не настроено, создайте резервную копию моментального снимка диска операционной системы.
- [Проверьте, является ли виртуальная машина поколением 1 или 2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Обновление с SUSE 12 SP4 или SP5 до SUSE 15 SP1

1. Установите последнюю версию пакета для виртуальной машины:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. После завершения установки перезапустите виртуальную машину.

3. Проверьте версию ядра и ОС. Убедитесь, что используется версия SUSE 12 SP4 или SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Установите пакет **suse-migration-sle15-activation**. После установки пакета **suse-migration-sle15-activation** автоматически установится другой пакет **SLES15-Migration** в качестве пакета зависимостей. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. После завершения установки запустите команду `reboot`, чтобы перезапустить виртуальную машину.

6. Перейдите на [портал Azure](https://portal.azure.com), выберите виртуальную машину, а затем — **Последовательная консоль**. Вы увидите, что система остановится и отобразится сообщение reboot: Restarting system (Перезагрузка: перезапуск системы). Это займет около 15–45 минут. Виртуальная машина поколения 2 может зависнуть с сообщением reboot: Restarting system (Перезагрузка: перезапуск системы). В этом случае подождите около 45 минут. Если это не помогло, перейдите к странице **обзора** виртуальной машины на портале Azure, а затем остановите и перезапустите виртуальную машину.

     ![Снимок экрана, посвященный сообщениям в последовательной консоли.](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. После перезапуска системы с новым ядром вы увидите следующее сообщение.

     ![Снимок экрана, посвященный сообщениям в последовательной консоли после перезапуска системы с новым ядром.](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Проверьте версию ядра и ОС, чтобы убедиться в успешности обновления системы.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Проверка версии поколения виртуальной машины

Для проверки версии поколения можно использовать один из следующих методов:

- В терминале SLES запустите команду `dmidecode | grep -i hyper`. Если это виртуальная машина поколения 1, выходные данные не возвращаются. Для виртуальных машин поколения 2 вы увидите такой результат:

     ![Снимок экрана выходных данных для виртуальной машины поколения 2](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- На [портале Azure](https://portal.azure.com) перейдите к **свойствам** виртуальной машины, а затем установите флажок в поле **Поколение виртуальной машины**.
