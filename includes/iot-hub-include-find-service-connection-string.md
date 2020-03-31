---
title: включить файл
description: включить файл
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558457"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Чтобы получить строку подключения Концентратора IoT для политики **службы,** выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите **группы ресурсов.** Выберите группу ресурсов, в которой находится концентратор, а затем выберите концентратор из списка ресурсов.

1. На левом боковом стене концентратора IoT выберите **политики общего доступа.**

1. Из списка политик выберите политику **службы.**

1. Под **общими ключами доступа**выберите значок копии для **строки Connection - основной ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Для получения дополнительной информации о правилах доступа и разрешениях IoT Hub можно [ознакомиться на элементах управления и разрешений Access.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
