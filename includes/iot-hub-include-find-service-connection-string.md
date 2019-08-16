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
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558457"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Чтобы получить строку подключения центра Интернета вещей для политики **службы** , выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите **группы ресурсов**. Выберите группу ресурсов, в которой находится центр, а затем выберите центр из списка ресурсов.

1. В левой части центра Интернета вещей выберите **политики общего доступа**.

1. В списке политик выберите политику **службы** .

1. В разделе **ключи общего доступа**выберите значок копирования для **строки подключения — первичный ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Дополнительные сведения о политиках и разрешениях общего доступа центра Интернета вещей см. в разделе [Управление доступом и разрешения](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
