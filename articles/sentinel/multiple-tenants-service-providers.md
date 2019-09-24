---
title: Работа с несколькими клиентами в Azure Sentinel для поставщиков услуг МССП Документация Майкрософт
description: Как работать с несколькими клиентами в Azure Sentinel для поставщиков услуг МССП.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240489"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Работа с несколькими клиентами в Azure Sentinel 

Если вы являетесь поставщиком управляемой службы безопасности (МССП) и используете [Azure лигхсаусе](../lighthouse/overview.md) для управления центрами деятельности по обеспечению безопасности клиентов, вы сможете управлять ресурсами-метками Azure ваших клиентов без прямого подключения к Клиент клиента из собственного клиента Azure. 

## <a name="prerequisites"></a>Предварительные требования
- [Подключение Лигхсаусе Azure](../lighthouse/how-to/onboard-customer.md)
- Для правильной работы клиент должен быть зарегистрирован в поставщике ресурсов Sentinel Azure по крайней мере в одной подписке. Если в вашем клиенте есть зарегистрированный Sentinel Azure, вы можете приступить к работе. Если нет, в портал Azure выберите **подписки** , а `Microsoft.Security.Insights` затем — **поставщики ресурсов** , а затем найдите и выберите **Register**.
   ![Проверка поставщиков ресурсов](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Как получить доступ к Azure Sentinel из других клиентов
1. В разделе **Каталог + подписка**выберите делегированные каталоги и подписки, в которых находятся рабочие области Sentinel Azure вашего клиента.

   ![Создание инцидентов безопасности](media/multiple-tenants-service-providers/directory-subscription.png)

1. Откройте Azure Sentinel. Вы увидите все рабочие области в выбранных подписках, и вы сможете работать с ними с легкостью, например с любой рабочей областью в вашем клиенте.

> [!NOTE]
> Вы не сможете развернуть соединители в Azure Sentinel из управляемой рабочей области. Чтобы развернуть соединитель, необходимо напрямую войти в клиент, на котором требуется развернуть соединитель, и выполнить аутентификацию с необходимыми разрешениями.





## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как легко управлять несколькими клиентами Sentinel Azure. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

