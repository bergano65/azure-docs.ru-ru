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
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: bc791fea1dfd184749e84cb7b7a912972c6a9f12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60657625"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Создание подписок в службе управления API Azure

При публикации API в службе управления API Azure самый простой способ защитить доступ к этим интерфейсам API — использовать ключи подписки. Клиентские приложения, которым требуется использовать опубликованные API, должны включать действительный ключ подписки в HTTP-запросах при выполнении вызовов к этим API. Чтобы получить ключ подписки для доступа к API, нужна подписка. См. дополнительные сведения о [подписках в службе управления API Azure](api-management-subscriptions.md).

Эта статья поможет вам создать подписки с помощью портала Azure.

## <a name="prerequisites"></a>Технические условия

Для выполнения действий в этой статье требуется следующее.

+ [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).
+ Общие сведения о [подписках в службе управления API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Создание подписки

1. Выберите **Подписки** в меню слева.
2. Выберите **Добавить подписку**.
3. Укажите название подписки и выберите область.
4. При необходимости выберите, если подписки должны быть связаны с пользователем.
5. Щелкните **Сохранить**.

![Гибкие подписки](./media/api-management-subscriptions/flexible-subscription.png)

После того как вы создадите подписку, предоставляются два ключа API для получения доступа к API-интерфейсам. Один ключ является основным, а второй — дополнительным. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о службе управления API:

+ Узнайте о терминологии Управления API в [этой статье](api-management-terminology.md).
+ Ознакомьтесь с [руководствами](import-and-publish.md) по использованию службы управления API.
+ Изучите страницу [Часто задаваемые вопросы о службе управления API Azure](api-management-faq.md).