---
title: включить файл
description: включить файл
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883751"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Чтобы получить строку подключения Концентратора IoT для политики **registryReadWrite,** выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите **группы ресурсов.** Выберите группу ресурсов, в которой находится концентратор, а затем выберите концентратор из списка ресурсов.

2. На левом боковом стене концентратора выберите **политики общего доступа.**

3. Из списка полисов выберите политику **registryReadWrite.**

4. Под **общими ключами доступа**выберите значок копии для **строки Connection - основной ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Для получения дополнительной информации о правилах доступа и разрешениях IoT Hub можно [ознакомиться на элементах управления и разрешений Access.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
