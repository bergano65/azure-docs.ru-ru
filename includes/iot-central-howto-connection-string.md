---
title: включение файла
description: включение файла
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151969"
---
1. Используйте `dps-keygen` программу командной строки для создания строки подключения:

    Чтобы установить [ключа программа создания](https://github.com/Azure/dps-keygen), выполните следующую команду:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Чтобы создать строку подключения, выполните следующую команду, используя сведения о подключении, которое вы записали ранее:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Скопируйте строку подключения из `dps-keygen` выходные данные для использования в коде устройства.