---
title: Создание или изменение прямого пиринга с помощью портала Azure
titleSuffix: Azure
description: Создание или изменение прямого пиринга с помощью портала Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681061"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Создание или изменение прямого пиринга с помощью портала Azure

В этой статье описывается, как создать пиринг Microsoft Direct с помощью портала Azure. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое руководство Direct peering](walkthrough-direct-all.md) перед началом конфигурации.
* Если у вас уже есть прямые вглядовые связи с корпорацией Майкрософт, которые не преобразованы в ресурсы Azure, [см. Преобразовать устаревающее прямое использование ресурса Azure с помощью портала.](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
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

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего вонючего Внеся в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).
