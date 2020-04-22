---
title: Устранение неполадок с учетной записью службы автоматизации
description: Узнайте, как устранить неполадки и устранить проблемы с учетной записью Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679381"
---
# <a name="troubleshoot-the-automation-account"></a>Устранение проблем аккаунта автоматизации

В этой статье рассматриваются решения проблем, с которыми вы можете столкнуться при использовании учетной записи Automation. В следующих разделах выделены конкретные сообщения об ошибках и возможные разрешения для каждого из них. Для получения общей информации об учетных записях Автоматизации [см.](../automation-quickstart-create-account.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Сценарий: Невозможно зарегистрировать поставщика ресурсов автоматизации для подписок

### <a name="issue"></a>Проблемы

Когда вы работаете с управленческими решениями в учетной записи Automation, вы сталкиваетесь со следующей ошибкой:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Причина:

Поставщик ресурсов автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Решение

Чтобы зарегистрировать поставщика ресурсов автоматизации, выполните следующие действия на портале Azure:

1. В браузере перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к **подписке** и выберите подписку со страницы Подписки.   

3. В **настройках**выберите **поставщиков ресурсов.**

4. Из списка поставщиков ресурсов убедитесь, что поставщик ресурсов **Microsoft.Automation** зарегистрирован.

5. Если поставщик не указан в списке, зарегистрируйте его, как описано в [ошибках Resolve для регистрации поставщика ресурсов.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="next-steps"></a>Следующие шаги

Если вы не видите проблему выше или не можете решить вашу проблему, попробуйте один из следующих каналов для дополнительной поддержки:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**