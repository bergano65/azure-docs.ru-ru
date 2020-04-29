---
title: Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портал Azure
titleSuffix: Azure
description: Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портал Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678539"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портал Azure

В этой статье описывается, как преобразовать имеющийся устаревший пиринг Exchange в ресурс Azure с помощью портал Azure.

При желании вы можете пройти это руководством с помощью [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Перед началом настройки ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по пирингу Exchange](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Преобразование устаревшего пиринга Exchange

Можно преобразовать устаревшие соединения пиринга с помощью ресурса **пиринга** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Проверка пиринга Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в разделе [часто задаваемые вопросы об пиринга через Интернет](faqs.md).

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
