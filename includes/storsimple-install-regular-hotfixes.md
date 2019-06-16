---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171877"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Установка обычных исправлений с помощью Windows PowerShell для StorSimple
1. Подключитесь к последовательной консоли устройства. Дополнительные сведения см. в разделе [Step 1: Connect to the serial console](../articles/storsimple/storsimple-update-device.md#step1) (Шаг 1. Подключение к последовательной консоли).
2. В меню последовательной консоли выберите вариант 1 **Войти с полным доступом**. Введите пароль. Пароль по умолчанию — **Password1**.
3. В командной строке выполните следующую команду:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Эта команда применяется только для обычных исправлений. Эта команда выполняется только на одном контроллере, но обновляться будут оба контроллера.
    > В процессе обновления может выполняться отработка отказа контроллера, но это не повлияет на доступность или работу системы.

4. При появлении запроса укажите путь к общей сетевой папке, содержащей файлы исправления.
5. После этого введите подтверждение для применения этих исправлений. Нажмите клавишу **Y** для продолжения установки исправления.

