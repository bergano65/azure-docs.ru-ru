---
title: Создание или изменение пиринга Exchange с помощью портал Azure
titleSuffix: Azure
description: Создание или изменение пиринга Exchange с помощью портал Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680942"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Создание или изменение пиринга Exchange с помощью портал Azure

В этой статье описывается, как создать пиринг Microsoft Exchange с помощью портал Azure. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете пройти это руководством с помощью [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Перед началом настройки ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по пирингу Exchange](walkthrough-exchange-all.md) .
* Если у вас уже есть пиринг Exchange с Майкрософт, которые не преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Создание и инициализация пиринга Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Создание пиринга Exchange

Новый запрос пиринга можно создать с помощью ресурса **пиринга** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Проверка пиринга Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Изменение пиринга Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Отменить подготовку пиринга Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
* [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в разделе [часто задаваемые вопросы об пиринга через Интернет](faqs.md).
