---
title: Создание или изменение прямого пиринга с помощью портал Azure
titleSuffix: Azure
description: Создание или изменение прямого пиринга с помощью портал Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681061"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Создание или изменение прямого пиринга с помощью портал Azure

В этой статье описывается, как создать прямой пиринг Майкрософт с помощью портал Azure. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить это пошаговое руководством с помощью Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Подготовка к работе
* Прежде чем начать настройку, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по непосредственному пирингу](walkthrough-direct-all.md) .
* Если у вас уже есть прямые пиринга с Майкрософт, которые не преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Войдите на портал и выберите свою подписку.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Создание прямого пиринга

Новый запрос пиринга можно создать с помощью ресурса **пиринга** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Запуск ресурса и настройка основных параметров
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Настройка подключений и отправка
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Отменить подготовку прямого пиринга
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения см. в разделе [часто задаваемые вопросы об пиринга через Интернет](faqs.md).
