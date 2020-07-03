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
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79140003"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Проверка предупреждений (тестовый файл EICAR) в Центре безопасности Azure
Этот документ содержит информацию о том, как убедиться, что ваша система правильно настроена для оповещений центра безопасности Azure.

## <a name="what-are-security-alerts"></a>Что такое оповещения системы безопасности?
Оповещения — это уведомления, которые Центр безопасности создает при обнаружении угроз для ресурсов. Он определяет приоритеты и отображает оповещения вместе с информацией, необходимой для быстрого изучения проблемы. Центр безопасности также предоставляет рекомендации по устранению атак.
Дополнительные сведения см. в статьях [оповещения системы безопасности в центре безопасности](security-center-alerts-overview.md) и [Управление оповещениями системы безопасности и реагирование на них](security-center-managing-and-responding-alerts.md) .

## <a name="alert-validation"></a>Проверка оповещений

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Проверка оповещений на виртуальных машинах Windows<a name="validate-windows"></a>

После установки агента Центра безопасности на компьютере выполните следующие действия на компьютере, на котором вы хотите использовать для этого предупреждение:

1. Скопируйте исполняемый файл (например **, Calc. exe**) на Рабочий стол компьютера или в другой каталог вашего удобства и переименуйте его как **ASC_AlertTest_662jfi039N. exe**.
1. Откройте командную строку и выполните этот файл с аргументом (просто поддельным именем аргумента), например:```ASC_AlertTest_662jfi039N.exe -foo```
1. Подождите 5–10 минут и откройте оповещения центра безопасности. Должно отобразиться предупреждение, аналогичное приведенному ниже [примеру](#alert-validate) .

> [!NOTE]
> При просмотре этого тестового оповещения для Windows убедитесь, что **включен аудит аргументов** поля **.** Если значение равно **false**, необходимо включить аудит аргументов командной строки. Чтобы включить его, используйте следующую команду:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Проверка оповещений на виртуальных машинах Linux<a name="validate-linux"></a>

После установки агента Центра безопасности на компьютере выполните следующие действия на компьютере, на котором вы хотите использовать для этого предупреждение:
1. Скопируйте исполняемый файл в удобное место и переименуйте его в **./asc_alerttest_662jfi039n**, например:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Откройте командную строку и выполните следующий файл:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Подождите 5–10 минут и откройте оповещения центра безопасности. Должно отобразиться предупреждение, аналогичное приведенному ниже [примеру](#alert-validate) .

### <a name="alert-example"></a>Пример предупреждения<a name="alert-validate"></a>

![Пример проверки оповещения](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Проверка оповещений в Kubernetes<a name="validate-kubernetes"></a>

Если вы используете предварительную версию центра безопасности для интеграции службы Kubernetes Azure, выполните следующую команду kubectl, чтобы проверить работоспособность ваших оповещений:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Дополнительные сведения об интеграции службы Azure Kubernetes и центра безопасности Azure см. в [этой статье](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Дальнейшие шаги
В этой статье представлен процесс проверки оповещений. Теперь, когда вы знакомы с проверкой, ознакомьтесь с такими статьями:

* [Проверка Azure Key Vault обнаружения угроз в центре безопасности Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Управление оповещениями безопасности в центре безопасности Azure и реагирование](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) на них — Узнайте, как управлять оповещениями и реагировать на инциденты безопасности в центре безопасности.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md). Узнайте, как отслеживать работоспособность ресурсов Azure.
* Сведения об [оповещениях системы безопасности в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) — сведения о различных типах оповещений системы безопасности.
