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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912475"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

В этой статье вы создадите серверную службу, которая добавляет требуемые свойства в двойника устройства, а затем запрашивает реестр удостоверений, чтобы найти все устройства с сообщаемыми свойствами, которые были обновлены соответствующим образом. Службе требуется разрешение на **Подключение к службе** , чтобы изменить требуемые свойства двойникаа устройства, а для запроса реестра удостоверений требуется разрешение на **Чтение реестра** . Политика общего доступа по умолчанию, которая содержит только эти два разрешения, не существует, поэтому необходимо создать ее.
