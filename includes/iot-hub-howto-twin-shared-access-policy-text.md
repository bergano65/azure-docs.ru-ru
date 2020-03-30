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
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050432"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

В этой статье вы создаете службу бэк-энда, которая добавляет желаемые свойства к близнецу устройства, а затем запрашивает реестр идентификации, чтобы найти все устройства с соответствующими свойствами, которые были обновлены соответствующим образом. Службе необходимо разрешение **на подключение службы** для изменения желаемых свойств близнеца устройства, и для запроса реестра идентификационных данных требуется разрешение **на чтение реестра.** Нет политики общего доступа по умолчанию, которая содержит только эти два разрешения, поэтому необходимо создать одну.
