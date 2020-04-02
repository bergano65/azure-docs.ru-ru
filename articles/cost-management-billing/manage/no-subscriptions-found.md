---
title: Ошибка "Подписки не найдены" при попытке входа на портал Azure
description: Из этой статьи вы узнаете, как устранить ошибку "Подписки не найдены", которая возникает при входе на портал Azure или в Центр управления учетной записью Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 76f109645e4a27e712066cec7010f2ac7eb6b507
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411646"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Ошибка "Подписки не найдены" при попытке входа на портал Azure или в Центр управления учетной записью Azure

При попытке войти на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) может появиться сообщение об ошибке "Подписки не найдены". В этой статье предлагается решение указанной проблемы.

## <a name="symptom"></a>Симптом

При попытке входа на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) отображается следующее сообщение об ошибке: "Подписки не найдены".

## <a name="cause"></a>Причина

Эта проблема возникает, если вы выбрали неправильный каталог или учетная запись не предоставляет необходимых разрешений.

## <a name="solution"></a>Решение

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Сценарий 1. Сообщение об ошибке получено на [портале Azure](https://portal.azure.com)

Чтобы устранить эту проблему:

* Убедитесь, что выбран надлежащий каталог Azure, щелкнув учетную запись в правом верхнем углу.

  ![Выберите каталог в верхней правой части портала Azure](./media/no-subscriptions-found/directory-switch.png)
* Если вы выбрали надлежащий каталог Azure, но по-прежнему получаете сообщение об ошибке, [назначьте учетной записи роль владельца](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Сценарий 2. Сообщение об ошибке получено в [Центре управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1. Войдите в [представление подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Выберите подписку, которую требуется проверить, а затем просмотрите раздел **Параметры**.
1. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .  

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
