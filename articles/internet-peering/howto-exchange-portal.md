---
title: Создание или изменение пиринга через точку обмена с помощью портала
titleSuffix: Azure
description: Создание или изменение пиринга через точку обмена с помощью портала
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774579"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Создание или изменение пиринга через точку обмена с помощью портала

В этой статье описывается, как создать пиринг биржи Майкрософт с помощью портала. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [обменпирирования пошаговое решение,](walkthrough-exchange-all.md) прежде чем начать конфигурацию.
* В случае, если у вас уже есть кондиционирования Exchange с корпорацией Майкрософт, которые не преобразуются в ресурсы Azure, обратитесь [к преобразованию устаревшего Ресурса Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Создание и предоставление пиринга Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Вопийте на портале и выберите подписку
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Создание пиринга Exchange

Вы можете создать новый запрос на пиринг с помощью ресурса **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка базовых настроек
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка соединений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Проверить врали биржи
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Изменение пиринга Биржи
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Деобеспечение биры пиринг
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
* [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)
