---
title: Проблема устранения azure IoT концентратор ошибка 409001 Устройствоужесуществует
description: Понять, как исправить ошибку 409001 УстройствоУжеСуществует
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960793"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

В этой статье описаны причины и решения для **409001 DeviceAlreadyExists** ошибок.

## <a name="symptoms"></a>Симптомы

При попытке зарегистрировать устройство в Концентраторе IoT запрос завершается ошибкой с ошибкой **409001 DeviceAlreadyExists.**

## <a name="cause"></a>Причина

В концентраторе IoT уже есть устройство с тем же идентификатором устройства. 

## <a name="solution"></a>Решение

Используйте другой идентификатор устройства и повторите попытку.