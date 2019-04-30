---
title: Обязательные требования для модуля Azure IoT Edge | Документация Майкрософт
description: Обязательные требования для публикации модуля IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910342"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Обязательные требования публикации модуля IoT Edge

В этой статье описаны обязательные требования, позволяющие опубликовать предложение модуля IoT Edge.  Если вы еще не сделано, просмотрите [модули Edge Интернета вещей, руководство по публикации](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Обязательные требования для публикации

Чтобы опубликовать модуль IoT Edge в Azure Marketplace, вам потребуется выполнить следующие условия:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Получите доступ к [порталу Cloud Partner](https://cloudpartner.azure.com/). Дополнительные сведения см. в [руководстве по публикации в Azure Marketplace и AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Согласитесь с [Условиями Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/).
- Разместите технические средства вашего модуля IoT Edge в Реестре контейнеров Azure.  Дополнительные сведения см. в разделе [Prepare your IoT Edge module technical assets](./cpp-create-technical-assets.md) (Подготовка технических средств модуля IoT Edge).
- Подготовьте к использованию метаданные модуля IoT Edge. Например подготовьте следующие ресурсы:
    - название;
    - описание (в формате HTML);
    - изображение логотипа (в формате PNG и в одном из фиксированных размеров, включая 40 x 40, 90 x 90, 115 x 115, 255 x 115 пикселей);
    - условия использования и политика конфиденциальности;
    - конфигурация модуля по умолчанию, которая содержит маршруты, требуемые свойства двойника, createOptions и переменные среды.
    - Модуль документации
    - контактные данные службы поддержки.


## <a name="next-steps"></a>Дальнейшие действия

При наличии [подготовлен ресурс-контейнер технические модуля IoT Edge](./cpp-create-technical-assets.md), можно приступать к [создания вашего предложения для модуля IoT Edge](./cpp-create-offer.md). 
