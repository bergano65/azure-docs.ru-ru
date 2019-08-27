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
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050432"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

В этой статье вы создадите серверную службу, которая добавляет требуемые свойства в двойника устройства, а затем запрашивает реестр удостоверений, чтобы найти все устройства с сообщаемыми свойствами, которые были обновлены соответствующим образом. Службе требуется разрешение на **Подключение к службе** , чтобы изменить требуемые свойства двойникаа устройства, а для запроса реестра удостоверений требуется разрешение на **Чтение реестра** . Политика общего доступа по умолчанию, которая содержит только эти два разрешения, не существует, поэтому необходимо создать ее.
