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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775060"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала

В этой статье описывается, как преобразовать существующий прямой пиринг пиринга в ресурс Azure с помощью портала.

При желании вы можете выполнить инструкции с помощью [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Перед началом работы
* Перед началом настройки проверьте [Предварительные требования](prerequisites.md) и [Пошаговое руководство по непосредственному пирингу](walkthrough-direct-all.md) .


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Преобразование устаревшего прямого пиринга в ресурс Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Преобразование устаревшего прямого пиринга

Можно преобразовать устаревшие подключения пиринга с помощью ресурса **пиринга** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md).
