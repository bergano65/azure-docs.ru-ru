---
title: включение файла
description: включение файла
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403983"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Чтобы создать политику общего доступа, которая предоставляет разрешения **Connect** и для **чтения реестра** , а затем получить строку подключения для этой политики, выполните следующие действия.

1. Откройте центр Интернета вещей в [портал Azure](https://portal.azure.com). Самый простой способ открыть центр Интернета вещей — выбрать **группы ресурсов**, выбрать группу ресурсов, в которой находится центр Интернета вещей, а затем выбрать центр Интернета вещей в списке ресурсов.

2. В левой части центра Интернета вещей выберите **политики общего доступа**.

3. В верхнем меню над списком политик выберите **Добавить**.

4. В области **Добавить политику общего доступа** введите описательное имя политики. Например: *сервицеандрегистриреад*. В разделе **разрешения**выберите **Реестр чтение** и **Подключение к службе**, а затем щелкните **создать**.

    ![Покажите, как добавить новую политику общего доступа](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. Вернитесь на панель **политики общего доступа** и выберите в списке политик новую политику.

6. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
