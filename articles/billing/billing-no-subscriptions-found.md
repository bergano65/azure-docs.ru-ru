---
title: Ошибка "Подписки не найдены" при попытке входа на портал Azure или в Центр управления учетной записью Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как устранить ошибку "Подписки не найдены", которая возникает при входе на портал Azure или в Центр управления учетной записью Azure.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: ac1956987b224417dde56014200add6cabb0e1df
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227441"
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Ошибка "Подписки не найдены" на портале Azure или в Центре управления учетной записью Azure

При попытке войти на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) может появиться сообщение об ошибке "Подписки не найдены". В этой статье предлагается решение указанной проблемы.

## <a name="symptom"></a>Симптом

При попытке входа на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) отображается следующее сообщение об ошибке: "Подписки не найдены".

## <a name="cause"></a>Причина:

Эта проблема возникает, если вы выбрали неправильный каталог или учетная запись не предоставляет необходимых разрешений. 

## <a name="solution"></a>Решение

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Сценарий 1. Сообщение об ошибке получено на [портале Azure](https://portal.azure.com)

Чтобы устранить эту проблему:

* Убедитесь, что выбран надлежащий каталог Azure, щелкнув учетную запись в правом верхнем углу.

  ![Выберите каталог в верхней правой части портала Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Если вы выбрали надлежащий каталог Azure, но по-прежнему получаете сообщение об ошибке, [назначьте учетной записи роль владельца](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Сценарий 2. Сообщение об ошибке получено в [Центре управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1. Войдите в [представление подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Выберите подписку, которую требуется проверить, а затем просмотрите раздел **Параметры**.
1. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .  

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409), которая поможет быстро устранить проблему. 
