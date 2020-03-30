---
title: Проверка оповещения (тестовый файл EICAR) в Центре безопасности Azure Документы Майкрософт
description: В этом документе вы ознакомитесь с процедурой проверки оповещений безопасности в Центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140003"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Проверка предупреждений (тестовый файл EICAR) в Центре безопасности Azure
Этот документ содержит информацию о том, как убедиться, что ваша система правильно настроена для оповещений центра безопасности Azure.

## <a name="what-are-security-alerts"></a>Что такое оповещения системы безопасности?
Оповещения — это уведомления, которые Центр безопасности создает при обнаружении угроз для ресурсов. Он распорядится приоритетами и перечисляет оповещения вместе с информацией, необходимой для быстрого расследования проблемы. Центр безопасности также предоставляет рекомендации по устранению атак.
Для получения дополнительной информации смотрите [оповещения безопасности в Центре безопасности](security-center-alerts-overview.md) и [управление и реагирование на оповещения безопасности](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Проверка оповещений

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Проверка оповещений на Windows VMs<a name="validate-windows"></a>

После установки агента Центра безопасности на вашем компьютере выполните следующие шаги с компьютера, где вы хотите быть атакованным ресурсом оповещения:

1. Скопируйте исполняемый (например **calc.exe)** на рабочий стол компьютера или другой каталог вашего удобства и переименовать его в **ASC_AlertTest_662jfi039N.exe.**
1. Откройте запрос команды и выполните этот файл с аргументом (просто поддельным названием аргумента), например:```ASC_AlertTest_662jfi039N.exe -foo```
1. Подождите 5–10 минут и откройте оповещения центра безопасности. Предупреждение, аналогичное [приведенным ниже,](#alert-validate) должно отображаться:

> [!NOTE]
> При просмотре этого тестового оповещения для Windows убедитесь, что поле **Аргументы Аудит Enabled** **является правдой.** Если это **ложно,** то вам нужно включить командной строке аргументы аудита. Чтобы включить его, используйте следующую команду:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Проверка оповещений на Linux VMs<a name="validate-linux"></a>

После установки агента Центра безопасности на вашем компьютере выполните следующие шаги с компьютера, где вы хотите быть атакованным ресурсом оповещения:
1. Копируйте исполняемое в удобном месте и переименуем его в **./asc_alerttest_662jfi039n,** например:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Откройте запрос команды и выполните этот файл:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Подождите 5–10 минут и откройте оповещения центра безопасности. Предупреждение, аналогичное [приведенным ниже,](#alert-validate) должно отображаться:

### <a name="alert-example"></a>Пример оповещения<a name="alert-validate"></a>

![Пример проверки оповещения](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Проверка оповещений о Кубернете<a name="validate-kubernetes"></a>

Если вы используете функцию предварительного просмотра Центра безопасности для интеграции службы Azure Kubernetes, запустите следующую команду kubectl для проверки работы отом:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Для получения дополнительной информации об интеграции службы Azure Kubernetes и Центра безопасности Azure смотрите [в этой статье](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье представлен процесс проверки оповещений. Теперь, когда вы знакомы с проверкой, ознакомьтесь с такими статьями:

* [Проверка обнаружения угроз в ключе Убежища Azure в Центре безопасности Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Управление и реагирование на оповещения безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - Узнайте, как управлять оповещениями и реагировать на инциденты безопасности в Центре безопасности.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* [Понимание предупреждений безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - Узнайте о различных типах предупреждений безопасности.
