---
title: Интеллектуальное обнаружение в Azure Application Insights — предстоящие изменения для получателей уведомлений по умолчанию | Документация Майкрософт
description: Мониторинг трассировок приложений с помощью Azure Application Insights для обнаружения необычных шаблонов в данных телеметрии трассировок.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61299007"
---
# <a name="smart-detection-e-mail-notification-change"></a>Изменение уведомлений по электронной почте для службы интеллектуального обнаружения

Чтобы учесть пожелания заказчиков, 1 апреля 2019 г. мы изменим роли по умолчанию для получателей уведомлений по электронной почте от службы интеллектуального обнаружения.

## <a name="what-is-changing"></a>Что изменяется?

Сейчас уведомления по электронной почте от службы интеллектуального обнаружения по умолчанию отправляются пользователям с такими ролями: _Владелец подписки_, _Участник подписки_ и _Читатель подписки_. Эти роли часто назначаются пользователям, которые не принимают активное участие в мониторинге и которым не нужно получать такие уведомления. Чтобы улучшить вашу работу, мы меняем параметры отправки уведомлений, и теперь по умолчанию они будут отправляться только пользователям с ролями [Читатель данных мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) и [Участник мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor).

## <a name="scope-of-this-change"></a>Область применения этого изменения

Это изменение повлияет на все правила интеллектуального обнаружения, за исключением следующих:

* Правила интеллектуального обнаружения с пометкой предварительной версии. Такие правила интеллектуального обнаружения сейчас не поддерживают уведомления по электронной почте.

* Правило аномальных сбоев. Это правило начнет срабатывать для новых ролей по умолчанию после переноса с платформы классических оповещений на платформу унифицированных оповещений (см. [дополнительные сведения](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)).

## <a name="how-to-prepare-for-this-change"></a>Как подготовиться к этому изменению?

Чтобы обеспечить отправку уведомлений по электронной почте от интеллектуального обнаружения для соответствующих пользователей, эти пользователи должны быть назначены [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) или [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ролей подписки.

Чтобы назначить пользователям роль читателя данных мониторинга или участника мониторинга на портале Azure, выполните действия, описанные в статье [Управление доступом с помощью RBAC и портала Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). Обязательно выберите вариант _Читатель данных мониторинга_ или _Участник мониторинга_ в качестве роли, назначаемой пользователю.

> [!NOTE]
> Определенные получатели уведомлений от службы интеллектуального обнаружения, настроенные с помощью параметра _Дополнительные получатели письма_ в параметрах правила, не будут затронуты этим изменением. Такие пользователи будут и дальше получать уведомления по электронной почте.

Если у вас есть вопросы или замечания по этому изменению, [свяжитесь с нами](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об интеллектуальном обнаружении:

- [Аномальные сбои](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Утечки памяти](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Аномалии производительности](../../azure-monitor/app/proactive-performance-diagnostics.md)