---
title: Предпосылки для модуля Azure IoT Edge Лазурный рынок
description: Обязательные требования для публикации модуля IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744981"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Обязательные требования публикации модуля IoT Edge

>[!Important]
>С 30 марта 2020 года мы начнем перемещение управления вашим модулем IoT Edge в Партнерский центр. После миграции вы будете создавать и управлять своими предложениями в Partner Center. Следуйте инструкциям в [совете по созданию модуля IoT Edge](https://aka.ms/AzureCreateIoT) для управления мигрированными предложениями.

В этой статье описаны обязательные требования, позволяющие опубликовать предложение модуля IoT Edge.  Если вы еще не сделали этого, просмотрите руководство по [публикации модулей IoT Edge.](../..//iot-edge-module.md)


## <a name="publishing-prerequisites"></a>Обязательные требования для публикации

Чтобы опубликовать модуль IoT Edge в Azure Marketplace, вам потребуется выполнить следующие условия:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Получите доступ к [порталу Cloud Partner](https://cloudpartner.azure.com/). Дополнительные сведения см. в [руководстве по публикации в Azure Marketplace и AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Согласитесь с [Условиями Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/).
- Разместите технические средства вашего модуля IoT Edge в Реестре контейнеров Azure.  Дополнительные сведения см. в разделе [Prepare your IoT Edge module technical assets](./cpp-create-technical-assets.md) (Подготовка технических средств модуля IoT Edge).
- Подготовьте к использованию метаданные модуля IoT Edge. Например, подготовьте следующие активы:
    - название;
    - описание (в формате HTML);
    - изображение логотипа (в формате PNG и в одном из фиксированных размеров, включая 40 x 40, 90 x 90, 115 x 115, 255 x 115 пикселей);
    - условия использования и политика конфиденциальности;
    - конфигурация модуля по умолчанию, которая содержит маршруты, требуемые свойства двойника, createOptions и переменные среды.
    - Модульная документация
    - контактные данные службы поддержки.


## <a name="next-steps"></a>Дальнейшие действия

Как только вы [подготовите технический актив модуля IoT Edge,](./cpp-create-technical-assets.md)вы будете готовы [создать модуль IoT Edge.](./cpp-create-offer.md) 
