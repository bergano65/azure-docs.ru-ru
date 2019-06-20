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
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185384"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

После создания центра получите для него строку подключения. С ее помощью устройства и приложения будут подключаться к центру. 

1. Щелкните центр, чтобы открыть область "Центр Интернета вещей" с параметрами и другими сведениями. Щелкните **Политики общего доступа**.
   
2. В разделе **Политики общего доступа** выберите политику **iothubowner**. 

3. В разделе **Ключи общего доступа** скопируйте значение **Строка подключения — первичный ключ** для дальнейшего использования.

    ![Получение строки подключения](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Дополнительные сведения см. в разделе [Контроль доступа](../articles/iot-hub/iot-hub-devguide-security.md) в руководстве разработчика для Центра Интернета вещей.
