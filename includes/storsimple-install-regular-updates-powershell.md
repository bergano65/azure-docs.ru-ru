---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165754"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Установка обычных обновлений с помощью Windows PowerShell для StorSimple
1. Откройте последовательную консоль устройства и выберите вариант 1 **Войти с полным доступом**. Введите пароль. Пароль по умолчанию — *Password1*. 
2. В командной строке выполните следующую команду:
   
     `Get-HcsUpdateAvailability`
   
    Вы получите уведомление о том, что обновления доступны, и о том, являются они критическими или некритическими.
3. В командной строке выполните следующую команду:
   
     `Start-HcsUpdate`
   
    Начнется процесс обновления.

> [!IMPORTANT]
> * Эта команда применяется только для обычных обновлений. Эта команда выполняется только на одном контроллере, но обновляться будут оба контроллера. 
> * В процессе обновления может выполняться отработка отказа контроллера, но это не повлияет на доступность или работу системы.
> 
> 

