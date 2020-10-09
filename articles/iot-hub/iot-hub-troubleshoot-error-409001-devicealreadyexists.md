---
title: Устранение неполадок центра Интернета вещей Azure 409001 Девицеалреадексистс
description: Сведения об исправлении ошибки 409001 Девицеалреадексистс
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960793"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

В этой статье описываются причины и решения для ошибок **409001 девицеалреадексистс** .

## <a name="symptoms"></a>Симптомы

При попытке зарегистрировать устройство в центре Интернета вещей происходит сбой запроса с ошибкой **409001 девицеалреадексистс**.

## <a name="cause"></a>Причина

В центре Интернета вещей уже имеется устройство с таким же ИДЕНТИФИКАТОРом. 

## <a name="solution"></a>Решение

Используйте другой идентификатор устройства и повторите попытку.