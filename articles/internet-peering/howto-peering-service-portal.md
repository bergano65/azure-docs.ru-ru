---
title: Включение прямого пиринга в Службе пиринга с помощью портала
titleSuffix: Azure
description: Включение прямого пиринга в Службе пиринга с помощью портала
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129975"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Включение прямого пиринга в Службе пиринга с помощью портала

В этой статье описывается, как включить [Peering Service](overview-peering-service.md) на прямой пиринг с помощью портала.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите предпосылки перед началом [настройки.](prerequisites.md)
* Выберите прямую вонючую в подписке, на что вы хотите включить службу пиринга. Если у вас его нет, либо конвертировать устаревшее прямое пиринг или создать новый Direct peering.
    * Чтобы преобразовать устаревшку Direct peering, следуйте инструкциям в [Convert a legacy Direct, вглядываясь в ресурс Azure с помощью портала.](howto-legacy-direct-portal.md)
    * Чтобы создать новый прямой пиринг, следуйте инструкциям в [Создать или изменить прямой пиринг с помощью портала](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение прямого пиринга в Службе пиринга

### <a name="view-direct-peering"></a><a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Включить прямое пиринговое обслуживание для peering Service

После открытия Direct peering на предыдущем этапе включите его для Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение прямого пирингового соединения

Если вам нужно изменить настройки соединения, обратитесь **к изменению** раздела Direct peering в [Create или изменении прямого пиринга с помощью портала](howto-direct-portal.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего пиринга через точку обмена в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для часто задаваемых вопросов, [см.](service-faqs.md)