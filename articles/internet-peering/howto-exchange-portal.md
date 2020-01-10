---
title: Создание или изменение пиринга Exchange с помощью портала
titleSuffix: Azure
description: Создание или изменение пиринга Exchange с помощью портала
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774579"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Создание или изменение пиринга Exchange с помощью портала

В этой статье описывается, как создать пиринг Microsoft Exchange с помощью портала. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить инструкции с помощью [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по Exchange пиринга](walkthrough-exchange-all.md) .
* Если у вас есть пиринг Exchange с корпорацией Майкрософт, которые не будут преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md) .

## <a name="create-and-provision-an-exchange-peering"></a>Создание и инициализация пиринга Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Создание пиринга Exchange

Новый запрос пиринга можно создать с помощью **однорангового** ресурса.

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Проверка пиринга Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Изменение пиринга Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Отменить подготовку пиринга Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md)
* [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)
