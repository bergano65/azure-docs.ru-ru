---
title: включить файл
description: включить файл
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756903"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Чтобы создать общую политику доступа, которая предоставляет **службу подключения** и **реестра написать** разрешения и получить строку соединения для этой политики, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите **группы ресурсов.** Выберите группу ресурсов, в которой находится концентратор, а затем выберите концентратор из списка ресурсов.

1. На левом боковом стене концентратора выберите **политики общего доступа.**

1. Из верхнего меню выше списка политик, **выберите Добавить**.

1. При **добавлении общей политики доступа**введите описательное имя для своей политики, например *serviceAndRegistryReadWrite.* Под **разрешениями**выберите запись и **обслуживание** **реестра,** а затем выберите **Создать.** (Разрешение на **чтение реестра** включается автоматически при выборе **записи реестра.)**

    ![Показать, как добавить новую политику общего доступа](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Выберите новую политику из списка политик.

1. Под **общими ключами доступа**выберите значок копии для **строки Connection - основной ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Для получения дополнительной информации о правилах доступа и разрешениях IoT Hub можно [ознакомиться на элементах управления и разрешений Access.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
