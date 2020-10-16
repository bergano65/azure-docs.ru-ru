---
title: Предварительные требования (общий файл для двух вкладок в статье) | Документация Майкрософт
description: Предварительные требования к заказу и настройке Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392490"
---
### <a name="for-service"></a>Для службы

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Для устройств

Перед тем как начать, убедитесь в следующем.

* Главный компьютер должен быть присоединен к сети центра обработки данных. Data Box будет копировать данные с этого компьютера. Главный компьютер должен запускать поддерживаемую операционную систему, как описано в разделе [Требования к системе Azure Data Box](../articles/databox/data-box-system-requirements.md).
* Центр обработки данных должен иметь высокоскоростную сеть. Настоятельно рекомендуем использовать хотя бы одно соединение Ethernet со скоростью передачи данных 10 Гбит/с. Если соединение Ethernet 10 Гбит/с недоступно, можно использовать канал передачи данных 1 Гбит/с, но это повлияет на скорость копирования.
