---
title: Устранение неполадок с учетной записью службы автоматизации
description: Узнайте, как устранять неполадки и устранять проблемы с учетной записью Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679381"
---
# <a name="troubleshoot-the-automation-account"></a>Устранение неполадок учетной записи службы автоматизации

В этой статье рассматриваются решения проблем, которые могут возникнуть при использовании учетной записи службы автоматизации. В следующих разделах выделяются конкретные сообщения об ошибках и возможные способы их устранения. Общие сведения об учетных записях службы автоматизации см. [в статье Создание учетной записи Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Сценарий: не удалось зарегистрировать поставщик ресурсов службы автоматизации для подписок

### <a name="issue"></a>Проблема

При работе с решениями для управления в учетной записи службы автоматизации возникает следующая ошибка:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Причина

Поставщик ресурсов службы автоматизации не зарегистрирован в подписке.

### <a name="resolution"></a>Решение

Чтобы зарегистрировать поставщик ресурсов службы автоматизации, выполните следующие действия в портал Azure.

1. В браузере перейдите на [портал Azure](https://portal.azure.com).

2. Перейдите к разделу **подписки** и выберите подписку на странице подписки.   

3. В разделе **Параметры**выберите **поставщики ресурсов**.

4. Убедитесь, что в списке поставщиков ресурсов зарегистрирован поставщик ресурсов **Microsoft. Automation** .

5. Если поставщик отсутствует в списке, зарегистрируйте его, как описано в разделе [Устранение ошибок при регистрации поставщика ресурсов](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите проблему выше или не можете устранить проблему, воспользуйтесь одним из следующих каналов для получения дополнительной поддержки:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport)помощью официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.