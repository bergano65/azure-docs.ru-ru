---
title: Предварительные требования для модуля Azure IoT Edge | Azure Marketplace
description: Обязательные требования для публикации модуля IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142371"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Обязательные требования публикации модуля IoT Edge

>[!Important]
>Начиная с 13 апреля 2020 г. Мы начинаем перемещаться по управлению предложениями модулей IoT Edge в центр партнеров. После миграции вы создадите предложения в центре партнеров и будете управлять ими. Следуйте инструкциям в разделе [создание IOT Edge модуля](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) для управления перенесенными предложениями.

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


## <a name="next-steps"></a>Дальнейшие шаги

После [подготовки технического ресурса IOT Edge модуля](./cpp-create-technical-assets.md)вы будете готовы к [созданию предложения модуля IOT Edge](./cpp-create-offer.md). 
