---
title: Управление учетной записью и ключами
titleSuffix: Language Understanding - Azure Cognitive Services
description: Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Учетные данные управляются на сайт account.microsoft.com. Ключ разработки управляется на странице параметров портала LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d5a1d7ee3b8b16631f7b919f3aece0848d662e62
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523517"
---
# <a name="manage-account-and-authoring-key"></a>Управление учетной записью и ключом разработки

Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Сведения для входа управляются с сайта [account.microsoft.com](https://account.microsoft.com). Ключ разработки управляется из [LUIS](luis-reference-regions.md) портала **параметры** страницы.

## <a name="authoring-key"></a>Ключ разработки

Этот единый, конкретного региона создания ключа на **параметры** странице, позволяет создавать все приложения из [LUIS](luis-reference-regions.md) портала, а также [разработка API-интерфейсов](https://go.microsoft.com/fwlink/?linkid=2092087). Для удобства ключу разработки разрешается запрашивать конечные точки на протяжении месяца [ограниченное](luis-boundaries.md) количество раз.

[![Страница параметров LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Ключ разработки используется для любых приложений, которыми вы владеете, так и для приложений, в которых вы отображены как сотрудник.

## <a name="authoring-key-regions"></a>Регионы ключа разработки

Ключ разработки соответствует [региону разработки](luis-reference-regions.md#publishing-regions). Для каждого региона существует свой уникальный ключ.

## <a name="reset-authoring-key"></a>Сброс ключа разработки

Если ключ разработки скомпрометирован, его необходимо сбросить. Для всех приложений в сбрасывается ключ [LUIS](luis-reference-regions.md) портала. При создании приложения через веб-сайт разработки API необходимо изменить значение `Ocp-Apim-Subscription-Key` на новый ключ.

## <a name="delete-account"></a>Удаление учетной записи

Дополнительные сведения о том, какие данные удаляются при удалении учетной записи см. статью [Data storage and removal](luis-concept-data-storage.md#accounts) (Хранение и удаление данных).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в разделе [Keys in LUIS](luis-concept-keys.md#authoring-key) (Ключи в службе LUIS).

