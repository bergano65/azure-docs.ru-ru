---
title: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала
titleSuffix: Azure
description: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775203"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Преобразование устаревшего пиринга через точку обмена в ресурс Azure с помощью портала

В этой статье описывается, как преобразовать существующую устаревшую биржу, вглядывающуюся в ресурс Azure с помощью портала.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [обменпирирования пошаговое решение,](walkthrough-exchange-all.md) прежде чем начать конфигурацию.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Преобразование устаревшего пиринга через точку обмена в ресурс Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Преобразование взаистого вонзательного замысла

Можно преобразовать устаревшие пиринговые соединения с помощью ресурса **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Проверить врали Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга через точку обмена с помощью портала](howto-exchange-portal.md)
