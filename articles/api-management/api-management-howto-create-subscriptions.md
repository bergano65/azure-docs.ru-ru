---
title: Создание подписок в службе управления API Azure | Документация Майкрософт
description: Узнайте, как создавать подписки в службе управления API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073542"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Создание подписок в службе управления API Azure

При публикации API в службе управления API Azure самый простой способ защитить доступ к этим интерфейсам API — использовать ключи подписки. Клиентские приложения, которым требуется использовать опубликованные API, должны включать действительный ключ подписки в HTTP-запросах при выполнении вызовов к этим API. Чтобы получить ключ подписки для доступа к API, нужна подписка. Дополнительные сведения о подписках см. [в статье подписки в службе управления API Azure](api-management-subscriptions.md).

Эта статья поможет вам создать подписки с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий в этой статье требуется следующее.

+ [Создайте экземпляр управления API](get-started-create-service-instance.md).
+ Общие сведения о [подписках в службе управления API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Создание подписки

1. Выберите **Подписки** в меню слева.
2. Выберите **Добавить подписку**.
3. Укажите название подписки и выберите область.
4. При необходимости выберите, следует ли связать подписку с пользователем.
5. Щелкните **Сохранить**.

![Гибкие подписки](./media/api-management-subscriptions/flexible-subscription.png)

После того как вы создадите подписку, предоставляются два ключа API для получения доступа к API-интерфейсам. Один ключ является основным, а второй — дополнительным. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о службе управления API:

+ Ознакомьтесь с другими [концепциями](api-management-terminology.md) в управлении API.
+ Чтобы узнать больше об управлении API, следуйте нашим [руководствам](import-and-publish.md) .
+ Часто задаваемые вопросы см. на [странице часто задаваемых](api-management-faq.md) вопросов.