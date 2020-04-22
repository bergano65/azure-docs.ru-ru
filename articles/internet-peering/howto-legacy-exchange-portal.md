---
title: Преобразование узловой биржи в ресурс Azure с помощью портала Azure
titleSuffix: Azure
description: Преобразование узловой биржи в ресурс Azure с помощью портала Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678539"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Преобразование узловой биржи в ресурс Azure с помощью портала Azure

В этой статье описывается, как преобразовать существующую устаревшую биржу в ресурс Azure с помощью портала Azure.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое пошаговое пошаговое пошаговое решение Exchange](walkthrough-exchange-all.md) перед началом конфигурации.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Преобразование наследного элемента Exchange в ресурс Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Преобразование взаистого вонзательного замысла

Вы можете преобразовать устаревшие пиринговые соединения с помощью ресурса **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Проверить врали Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
