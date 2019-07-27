---
title: Управление учетной записью и ключами — LUIS
titleSuffix: Azure Cognitive Services
description: Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Данные для входа управляются по адресу account.microsoft.com. Ваш ключ разработки управляется на странице параметров портала LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9164c94886488ac7dcadeb7894453daad5d8b322
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560491"
---
# <a name="manage-account-and-authoring-key"></a>Управление учетной записью и ключом разработки

Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Сведения для входа управляются с сайта [account.microsoft.com](https://account.microsoft.com). Ваш ключ разработки управляется на странице **параметров** портала [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Ключ разработки

Этот один и тот же ключ разработки, зависящий от региона, на странице **Параметры** позволяет создавать все приложения на портале [Luis](luis-reference-regions.md) , а также в [API-интерфейсах разработки](https://go.microsoft.com/fwlink/?linkid=2092087). Для удобства ключу разработки разрешается запрашивать конечные точки на протяжении месяца [ограниченное](luis-boundaries.md) количество раз.

[![Страница параметров LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Ключ разработки используется для любых приложений, которыми вы владеете, так и для приложений, в которых вы отображены как сотрудник.

## <a name="authoring-key-regions"></a>Регионы ключа разработки

Ключ разработки соответствует [региону разработки](luis-reference-regions.md#publishing-regions). Для каждого региона существует свой уникальный ключ.

## <a name="reset-authoring-key"></a>Сброс ключа разработки

Если ключ разработки скомпрометирован, его необходимо сбросить. Ключ сбрасывается для всех приложений на портале [Luis](luis-reference-regions.md) . При создании приложения через веб-сайт разработки API необходимо изменить значение `Ocp-Apim-Subscription-Key` на новый ключ.

## <a name="delete-account"></a>Удаление учетной записи

Дополнительные сведения о том, какие данные удаляются при удалении учетной записи см. статью [Data storage and removal](luis-concept-data-storage.md#accounts) (Хранение и удаление данных).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в разделе [Keys in LUIS](luis-concept-keys.md#authoring-key) (Ключи в службе LUIS).

