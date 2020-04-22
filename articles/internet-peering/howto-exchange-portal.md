---
title: Создание или изменение вонючего Exchange с помощью портала Azure
titleSuffix: Azure
description: Создание или изменение вонючего Exchange с помощью портала Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680942"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Создание или изменение вонючего Exchange с помощью портала Azure

В этой статье описывается, как создать пиринг биржи Майкрософт с помощью портала Azure. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое пошаговое пошаговое пошаговое решение Exchange](walkthrough-exchange-all.md) перед началом конфигурации.
* Если у вас уже есть пиринги Exchange с корпорацией Майкрософт, которые не преобразованы в ресурсы Azure, [см. Преобразование устаревшего ресурса Azure с помощью портала.](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Создание и предоставление пиринга Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Создание пиринга Exchange

Вы можете создать новый запрос на пиринг с помощью ресурса **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Проверить врали биржи
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Изменение пиринга Биржи
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Деобеспечение биры пиринг
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
* [Преобразование устаревшего прямого вонючего в ресурс Azure с помощью портала](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).
