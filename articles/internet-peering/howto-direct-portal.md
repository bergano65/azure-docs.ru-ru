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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775333"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Создание или изменение прямого пиринга с помощью портала

В этой статье описывается создание прямого пиринга Майкрософт с помощью портала. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить инструкции с помощью [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Перед началом работы
* Перед началом настройки проверьте [Предварительные требования](prerequisites.md) и [Пошаговое руководство по непосредственному пирингу](walkthrough-direct-all.md) .
* Если вы уже используете прямой пиринг с корпорацией Майкрософт, который не преобразуется в ресурсы Azure, см. статью [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md) .

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Создание прямого пиринга

Новый запрос пиринга можно создать с помощью **однорангового** ресурса.

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Отменить подготовку прямого пиринга
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md).
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)
