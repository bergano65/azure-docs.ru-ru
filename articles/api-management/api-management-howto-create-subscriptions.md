---
title: Создание подписок в службе управления API Azure | Документация Майкрософт
description: Узнайте, как создавать подписки в службе управления API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621736"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Создание подписок в службе управления API Azure

При публикации API в службе управления API (APIM) самый простой способ защитить доступ к этим интерфейсам API — использовать ключи подписки. Другими словами клиентские приложения, которым требуется использовать опубликованные API, должны включать действительный ключ подписки в HTTP-запросах при выполнении вызовов к этим API. Чтобы получить ключ подписки для доступа к API, нужна подписка. См. дополнительные сведения о [подписках в службе управления API Azure](api-management-subscriptions.md).

Эта статья поможет вам создать подписки с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения задач из этой статьи необходимо следующее:

+ [Создать экземпляр APIM](get-started-create-service-instance.md).
+ Получить представление о [подписках в APIM](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Создание подписки

1. Щелкните **Подписки** в меню слева.
2. Щелкните **Добавить подписку**.
3. Укажите название подписки и выберите область.
4. Щелкните **Сохранить**.

![Гибкие подписки](./media/api-management-subscriptions/flexible-subscription.png)

После создания подписки пары ключей API (первичный и вторичный) будут подготовлены для доступа к API.

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения о службе управления API:

+ См. сведения о других [концепциях](api-management-terminology.md) службы управления API.
+ См. [руководства](import-and-publish.md) по использованию службы управления API.
+ См. [страницу вопросов и ответов](api-management-faq.md) с информацией о распространенных проблемах.