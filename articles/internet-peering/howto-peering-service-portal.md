---
title: Включить службу пиринга Azure на прямой пиринг с помощью портала Azure
titleSuffix: Azure
description: Включить службу пиринга Azure на прямой пиринг с помощью портала Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687060"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Включить службу пиринга Azure на прямой пиринг с помощью портала Azure

В этой статье описывается, как включить [службу пиринга](overview-peering-service.md) Azure в прямой вонючий с помощью портала Azure.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) перед началом конфигурации.
* Выберите прямую вонючую в подписке, для которой вы хотите включить Peering Service. Если у вас его нет, либо конвертировать устаревшее прямое пиринг или создать новый прямой пиринг:
    * Чтобы преобразовать устаревшку Direct peering, следуйте инструкциям в [Convert a legacy Direct, вглядываясь в ресурс Azure с помощью портала.](howto-legacy-direct-portal.md)
    * Чтобы создать новый прямой пиринг, следуйте инструкциям в [Создать или изменить прямой пиринг с помощью портала](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение прямого пиринга в Службе пиринга

### <a name="view-direct-peering"></a><a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Включить прямое пиринговое обслуживание для peering Service

После открытия прямого пиринга на предыдущем этапе включите его для Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение прямого пирингового соединения

Чтобы изменить настройки соединения, см. раздел "Изменить прямое пиринг" в [разделе Создать или изменить прямое пиринг с помощью портала.](howto-direct-portal.md)

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего вонючего Внеся в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для часто задаваемых вопросов, [см.](service-faqs.md)