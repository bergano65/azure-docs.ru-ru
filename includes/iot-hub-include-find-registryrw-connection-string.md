---
title: включение файла
description: включение файла
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883751"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Чтобы получить строку подключения центра Интернета вещей для политики **registryReadWrite** , выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите **группы ресурсов**. Выберите группу ресурсов, в которой находится центр, а затем выберите центр из списка ресурсов.

2. В области слева в центре выберите **политики общего доступа**.

3. В списке политик выберите политику **registryReadWrite** .

4. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
