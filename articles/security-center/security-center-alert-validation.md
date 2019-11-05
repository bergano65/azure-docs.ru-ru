---
title: Проверка предупреждений (файл теста EICAR) в центре безопасности Azure | Документация Майкрософт
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
ms.openlocfilehash: d3ea0adb4adf88e557ca360799836555cb90078e
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520945"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Проверка предупреждений (файл теста EICAR) в центре безопасности Azure
Этот документ содержит информацию о том, как убедиться, что ваша система правильно настроена для оповещений центра безопасности Azure.

## <a name="what-are-security-alerts"></a>Что такое оповещения системы безопасности?
Оповещения — это уведомления, которые центр безопасности создает при обнаружении угроз для ресурсов. Он определяет приоритеты и отображает оповещения вместе с информацией, необходимой для быстрого изучения проблемы. Центр безопасности также предоставляет рекомендации по устранению атак.
Дополнительные сведения см. в статьях [оповещения системы безопасности в центре безопасности](security-center-alerts-overview.md) и [Управление оповещениями системы безопасности и реагирование на них](security-center-managing-and-responding-alerts.md) .

## <a name="alert-validation"></a>Проверка оповещений

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Проверка оповещений на виртуальной машине Windows<a name="validate-windows"></a>

После установки агента Центра безопасности на компьютере выполните следующие действия на компьютере, на котором вы хотите использовать для этого предупреждение:

1. Скопируйте исполняемый файл (например **, Calc. exe**) на Рабочий стол компьютера или в другой каталог вашего удобства и переименуйте его как **ASC_AlertTest_662jfi039N. exe**.
1. Откройте командную строку и выполните этот файл с аргументом (просто поддельным именем аргумента), например: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Подождите 5–10 минут и откройте оповещения центра безопасности. Должно отобразиться предупреждение, аналогичное приведенному ниже [примеру](#alert-validate) .

> [!NOTE]
> При просмотре этого тестового оповещения для Windows убедитесь, что **включен аудит аргументов** поля **.** Если значение равно **false**, необходимо включить аудит аргументов командной строки. Чтобы включить его, используйте следующую команду:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Проверка оповещений на виртуальной машине Linux<a name="validate-linux"></a>

После установки агента Центра безопасности на компьютере выполните следующие действия на компьютере, на котором вы хотите использовать для этого предупреждение:
1. Скопируйте исполняемый файл в удобное место и переименуйте его в **./asc_alerttest_662jfi039n**, например:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Откройте командную строку и выполните следующий файл:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Подождите 5–10 минут и откройте оповещения центра безопасности. Должно отобразиться предупреждение, аналогичное приведенному ниже [примеру](#alert-validate) .

### Пример предупреждения<a name="alert-validate"></a>

![Пример проверки оповещения](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Проверка оповещений в Kubernetes<a name="validate-kubernetes"></a>

Если вы используете предварительную версию центра безопасности для интеграции службы Kubernetes Azure, выполните следующую команду kubectl, чтобы проверить работоспособность ваших оповещений:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Дополнительные сведения об интеграции службы Azure Kubernetes и центра безопасности Azure см. в [этой статье](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье представлен процесс проверки оповещений. Теперь, когда вы знакомы с проверкой, ознакомьтесь с такими статьями:

* [Управление оповещениями безопасности в центре безопасности Azure и реагирование](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) на них — Узнайте, как управлять оповещениями и реагировать на инциденты безопасности в центре безопасности.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* Сведения об [оповещениях системы безопасности в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) — сведения о различных типах оповещений системы безопасности.
* [Руководство по устранению неполадок в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) . Узнайте, как устранять распространенные проблемы в центре безопасности.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md). Часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](https://blogs.msdn.com/b/azuresecurity/) . Найдите записи в блоге о безопасности и соответствии требованиям в Azure.
