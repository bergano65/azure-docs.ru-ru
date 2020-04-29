---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67185815"
---
Выполните действия в соответствии с конкретными обстоятельствами.

### <a name="unregister-a-connected-process-server"></a>Отмена регистрации подключенного сервера обработки

1. Установите удаленное подключение к серверу обработки от имени администратора.
2. На **панели управления**откройте " **программы" > удалить программу**.
3. Удалите программу **Microsoft Azure Site Recovery службы Mobility Service/главный целевой сервер**.
4. Удалите программу **Microsoft Azure Site Recovery Configuration/Process Server**.
5. После удаления программ, описанных в шагах 3 и 4, удалите **Microsoft Azure Site Recovery конфигурация и зависимости сервера обработки**.

### <a name="unregister-a-disconnected-process-server"></a>Отмена регистрации отключенного сервера обработки

Используйте эти действия, только если нет способа восстановить компьютер, на котором установлен сервер обработки.

1. Войдите на сервер конфигурации с правами администратора.
2. Откройте командную строку администратора и перейдите к `%ProgramData%\ASR\home\svsystems\bin`.
3. Выполните эту команду, чтобы получить список из одного или нескольких серверов обработки.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Х. Нет: серийный номер сервера обработки.
    - IP/Name: IP-адрес и имя компьютера, на котором выполняется сервер обработки.
    - Пульс: последние пакеты пульса с компьютера сервера обработки.
    ![Команда отмены регистрации](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Укажите серийный номер сервера обработки, регистрация которого будет отменена.
5. Отмена регистрации сервера обработки удаляет все сведения из системы и отображает сообщение: **успешно отменена регистрация имени сервера> (Server-IP-Address)**

