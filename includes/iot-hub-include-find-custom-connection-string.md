---
title: включить файл
description: включить файл
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050407"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Чтобы создать общую политику доступа, которая предоставляет **подключение службы** и **разрешение реестра** и получить строку подключения для этой политики, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите **группы ресурсов.** Выберите группу ресурсов, в которой находится концентратор, а затем выберите концентратор из списка ресурсов.

1. На левом боковом стене концентратора выберите **политики общего доступа.**

1. Из верхнего меню выше списка политик, **выберите Добавить**.

1. При **добавлении общей политики доступа**введите описательное имя для своей политики, например *serviceAndRegistryRead.* Под **разрешениями**выберите **регистрационное чтение** и **подключение службы,** а затем выберите **Создать.**

    ![Показать, как добавить новую политику общего доступа](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Выберите новую политику из списка политик.

1. Под **общими ключами доступа**выберите значок копии для **строки Connection - основной ключ** и сохраните значение.

    ![Получение строки подключения](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Для получения дополнительной информации о правилах доступа и разрешениях IoT Hub можно [ознакомиться на элементах управления и разрешений Access.](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)
