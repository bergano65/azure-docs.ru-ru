---
title: Предварительные требования для модуля Azure IoT Edge | Azure Marketplace
description: Обязательные требования для публикации модуля IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813861"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Обязательные требования публикации модуля IoT Edge

В этой статье описаны обязательные требования, позволяющие опубликовать предложение модуля IoT Edge.  Если вы еще не сделали это, ознакомьтесь с [руководством по публикации модулей IOT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Обязательные требования для публикации

Чтобы опубликовать модуль IoT Edge в Azure Marketplace, вам потребуется выполнить следующие условия:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Получите доступ к [порталу Cloud Partner](https://cloudpartner.azure.com/). Дополнительные сведения см. в [руководстве по публикации в Azure Marketplace и AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Согласитесь с [Условиями Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/).
- Разместите технические средства вашего модуля IoT Edge в Реестре контейнеров Azure.  Дополнительные сведения см. в разделе [Prepare your IoT Edge module technical assets](./cpp-create-technical-assets.md) (Подготовка технических средств модуля IoT Edge).
- Подготовьте к использованию метаданные модуля IoT Edge. Например, подготовьте следующие ресурсы:
    - название;
    - описание (в формате HTML);
    - изображение логотипа (в формате PNG и в одном из фиксированных размеров, включая 40 x 40, 90 x 90, 115 x 115, 255 x 115 пикселей);
    - условия использования и политика конфиденциальности;
    - конфигурация модуля по умолчанию, которая содержит маршруты, требуемые свойства двойника, createOptions и переменные среды.
    - Документация по модулю
    - контактные данные службы поддержки.


## <a name="next-steps"></a>Дальнейшие действия

После [подготовки технического ресурса IOT Edge модуля](./cpp-create-technical-assets.md)вы будете готовы к [созданию предложения модуля IOT Edge](./cpp-create-offer.md). 
