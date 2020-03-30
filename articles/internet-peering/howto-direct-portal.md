---
title: Создание или изменение прямого пиринга с помощью портала
titleSuffix: Azure
description: Создание или изменение прямого пиринга с помощью портала
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775333"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Создание или изменение прямого пиринга с помощью портала

В этой статье описывается, как создать microsoft Direct пиринг с помощью портала. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [прямое пошаговое руководство](walkthrough-direct-all.md) перед началом конфигурации.
* В случае, если у вас уже есть Direct peering с microsoft, которые не преобразуются в ресурсы Azure, обратитесь [к Преобразованию устаревшего Прямого вглянувшего в ресурс Azure с помощью портала](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Создание прямого пиринга

Вы можете создать новый запрос на пиринг с помощью ресурса **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверить прямой пиринг
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Деобеспечение прямого пиринга
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md).
* [Преобразование наследного Exchange в ресурс Azure с помощью портала.](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)
