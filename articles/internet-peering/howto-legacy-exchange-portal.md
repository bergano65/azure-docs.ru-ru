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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775203"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала

В этой статье описывается, как преобразовать имеющийся устаревший пиринг Exchange в ресурс Azure с помощью портала.

При желании вы можете выполнить инструкции с помощью [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по Exchange пиринга](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Преобразование устаревшего пиринга Exchange

Можно преобразовать устаревшие подключения пиринга с помощью ресурса **пиринга** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Проверка пиринга Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
