---
title: включить файл
description: включить файл
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017531"
---
При запуске примера приложения для устройства на более позднем шаге этого руководства вам потребуются следующие значения конфигурации:

* Область идентификатора: В приложении IoT Central перейдите к пункту **Администрирование > Подключение устройства**. Запишите значение **области идентификатора**.
* Первичный ключ группы: В приложении IoT Central перейдите к пункту **Администрирование > Подключение устройства > SAS-IoT-Devices**. Запишите значение **первичного ключа** подписанного URL-адреса.

Используйте Cloud Shell, чтобы создать ключ устройства из только что полученного ключа SAS группы:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Запишите созданный ключ устройства. Он понадобится на более позднем этапе работы с этим учебником.
