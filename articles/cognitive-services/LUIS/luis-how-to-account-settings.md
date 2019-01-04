---
title: Управление параметрами
titleSuffix: Language Understanding - Azure Cognitive Services
description: Управляйте настройками учетной записи пользователя и ключом разработки всех своих приложений с помощью веб-сайта LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: bd6ae88834b45e9e154eb1e5e3ba921f403c7eaa
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138761"
---
# <a name="manage-account-and-authoring-key"></a>Управление учетной записью и ключом разработки
Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Сведения для входа управляются с сайта [account.microsoft.com](https://account.microsoft.com). Ключ разработки управляется из веб-сайта [LUIS](luis-reference-regions.md) со страницы **Параметры**. 

## <a name="authoring-key"></a>Ключ разработки

Этот единый ключ разработки для конкретного региона на странице **Параметры**, который позволяет разрабатывать все приложения на веб-сайте [LUIS](luis-reference-regions.md) так же, как на веб-сайте по [разработке API](https://aka.ms/luis-authoring-api). Для удобства ключу разработки разрешается запрашивать конечные точки на протяжении месяца [ограниченное](luis-boundaries.md) количество раз. 

[![Страница параметров LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Ключ разработки используется для любых приложений, которыми вы владеете, так и для приложений, в которых вы отображены как сотрудник.

## <a name="authoring-key-regions"></a>Регионы ключа разработки
Ключ разработки соответствует [региону разработки](luis-reference-regions.md#publishing-regions). Для каждого региона существует свой уникальный ключ. 

## <a name="reset-authoring-key"></a>Сброс ключа разработки
Если ключ разработки скомпрометирован, его необходимо сбросить. На веб-сайте [LUIS](luis-reference-regions.md) можно сбросить ключ для всех приложений. При создании приложения через веб-сайт разработки API необходимо изменить значение `Ocp-Apim-Subscription-Key` на новый ключ. 

## <a name="delete-account"></a>Удаление учетной записи
Дополнительные сведения о том, какие данные удаляются при удалении учетной записи см. статью [Data storage and removal](luis-concept-data-storage.md#accounts) (Хранение и удаление данных). 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в разделе [Keys in LUIS](luis-concept-keys.md#authoring-key) (Ключи в службе LUIS). 

