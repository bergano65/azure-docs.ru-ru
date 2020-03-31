---
title: Автоматизация устранения неполадок учетной записи
description: Узнайте, как устранить неполадки и устранить проблемы с учетной записью Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301009"
---
# <a name="automation-account-troubleshooting"></a>Автоматизация устранения неполадок учетной записи

В этой статье рассматриваются решения проблем, с которыми вы можете столкнуться при использовании учетной записи Automation. В следующих разделах выделены конкретные сообщения об ошибках и возможные разрешения для каждого из них. Для получения общей информации об учетных записях Автоматизации [см.](../automation-quickstart-create-account.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Сценарий: Невозможно зарегистрировать поставщика ресурсов автоматизации для подписок

### <a name="issue"></a>Проблема

Когда вы работаете с управленческими решениями в учетной записи Automation, вы сталкиваетесь со следующей ошибкой:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Причина

Поставщик ресурсов автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Решение

Чтобы зарегистрировать поставщика ресурсов автоматизации, выполните следующие действия на портале Azure:

1. В браузере перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к **подписке** и выберите подписку со страницы Подписки.   

3. В **настройках**выберите **поставщиков ресурсов.**

4. Из списка поставщиков ресурсов убедитесь, что поставщик ресурсов **Microsoft.Automation** зарегистрирован.

5. Если он не указан, зарегистрируйте поставщика **Microsoft.Automation,** выполнив следующие действия по [ошибкам Resolve для регистрации поставщика ресурсов.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не увидели проблему или не можете решить проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**