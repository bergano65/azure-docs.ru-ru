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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775060"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала

В этой статье описывается, как преобразовать существующее устаревшем вглядывание в ресурс Azure с помощью портала.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [прямое пошаговое руководство](walkthrough-direct-all.md) перед началом конфигурации.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Преобразование устаревшего вонючеги для ресурса Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Преобразование устаревшего прямого пиринга

Можно преобразовать устаревшие пиринговые соединения с помощью ресурса **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверить прямой пиринг
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создайте или измените прямое пиринг с помощью портала.](howto-direct-portal.md)
