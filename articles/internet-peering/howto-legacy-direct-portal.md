---
title: Преобразование устаревшего прямого вонючего в ресурс Azure с помощью портала Azure
titleSuffix: Azure
description: Преобразование устаревшего прямого вонючего в ресурс Azure с помощью портала Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678827"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Преобразование устаревшего прямого вонючего в ресурс Azure с помощью портала Azure

В этой статье описывается, как преобразовать существующее устаревшем вглядывание в ресурс Azure с помощью портала Azure.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое руководство Direct peering](walkthrough-direct-all.md) перед началом конфигурации.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Преобразование устаревшего вонючего в ресурс Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Преобразование устаревшего прямого пиринга

Вы можете преобразовать устаревшие пиринговые соединения с помощью ресурса **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверить прямой пиринг
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
