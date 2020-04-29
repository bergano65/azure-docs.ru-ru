---
title: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портал Azure
titleSuffix: Azure
description: Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портал Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678827"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портал Azure

В этой статье описывается, как преобразовать существующий прямой пиринг в ресурс Azure с помощью портал Azure.

При желании вы можете пройти это руководством с помощью [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Прежде чем начать настройку, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по непосредственному пирингу](walkthrough-direct-all.md) .


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Преобразование устаревшего прямого пиринга в ресурс Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Преобразование устаревшего прямого пиринга

Можно преобразовать устаревшие соединения пиринга с помощью ресурса **пиринга** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в разделе [часто задаваемые вопросы об пиринга через Интернет](faqs.md).

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
