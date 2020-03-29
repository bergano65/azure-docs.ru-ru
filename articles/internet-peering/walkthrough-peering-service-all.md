---
title: Пошаговое руководство по Службе пиринга для партнеров
titleSuffix: Azure
description: Пошаговое руководство по Службе пиринга для партнеров
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720254"
---
# <a name="peering-service-partner-walkthrough"></a>Пошаговое руководство по Службе пиринга для партнеров

В этом разделе разъясняется, какие шаги должен выполнять поставщик, чтобы включить прямую пиринговую службу для Peering Service.

## <a name="create-direct-peering-connection-for-peering-service"></a>Создание прямого пирингового соединения для peering Service
Поставщики услуг могут расширить свой географический охват, создав новые прямые пиринговые работы, поддерживающие Peering Service. Для достижения этой цели
1. Станьте партнером Peering Service, если не
1. Следуйте инструкциям по [созданию или изменению прямого пиринга с помощью портала.](howto-direct-portal.md) Убедитесь, что он отвечает требованиям высокой доступности.
1. Затем выполните шаги, чтобы [включить peering службы на прямой пиринг с помощью портала](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Используйте устаревающее прямое пиринговое соединение для Peering Service
Если у вас есть наследие Direct peering, которое вы хотите использовать для поддержки Peering Service,
1. Стать партнером Peering Service, если не уже.
1. Следуйте инструкциям [по преобразованию устаревшего прямого вглядывания в ресурс Azure с помощью портала.](howto-legacy-direct-portal.md) При необходимости закажите дополнительные схемы, чтобы соответствовать требованиям высокой доступности.
1. Затем выполните шаги, чтобы [включить peering службы на прямой пиринг с помощью портала](howto-peering-service-portal.md).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о [политике пиринга](https://peering.azurewebsites.net/peering).
* Чтобы узнать о шагах по настройке Прямого пиринга с Microsoft, следуйте [Direct пиринг пошаговое руководство](walkthrough-direct-all.md).
* Чтобы узнать о шагах по настройке Exchange, чтобы сделать пиринг с помощью Корпорации Майкрософт, следуйте [пошаговым данным Exchange.](walkthrough-exchange-all.md)