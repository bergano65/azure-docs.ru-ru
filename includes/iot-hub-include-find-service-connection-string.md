---
title: включение файла
description: включение файла
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402373"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Чтобы получить строку подключения центра Интернета вещей для политики **службы** , выполните следующие действия.

1. Откройте центр Интернета вещей в [портал Azure](https://portal.azure.com).  Самый простой способ открыть центр Интернета вещей — выбрать **группы ресурсов**, выбрать группу ресурсов, в которой находится центр Интернета вещей, а затем выбрать центр Интернета вещей в списке ресурсов.

2. В левой части центра Интернета вещей выберите **политики общего доступа**.

3. В списке политик выберите политику **службы** .

4. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
