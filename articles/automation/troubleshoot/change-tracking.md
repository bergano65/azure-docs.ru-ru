---
title: Устранение неполадок в службе Отслеживания изменений Azure
description: В этой статье приводятся сведения об устранении неполадок в службе Отслеживания изменений.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564846"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Устранение неполадок в службах "Отслеживание изменений" и "Учет"

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Сценарий. Записи Отслеживания изменений не отображаются для компьютеров Windows

#### <a name="issue"></a>Проблемы

Вы не видите никаких результатов от служб "Учет" или "Отслеживание изменений" для компьютеров Windows, которые подключены к Отслеживанию изменений.

#### <a name="cause"></a>Причина:

Ошибка может быть вызвана следующими причинами.

1. Не работает **Microsoft Monitoring Agent**.
2. Обмен данными в учетной записи службы автоматизации заблокирован.
3. Не скачаны пакеты управления для Отслеживания изменений.
4. Подключенная виртуальная машина, возможно, поступила с клонированного компьютера, необработанного командой Sysprep с помощью установленного Microsoft Monitoring Agent.

#### <a name="resolution"></a>Разрешение

1. Убедитесь, выполняется ли на компьютере **Microsoft Monitoring Agent** (HealthService.exe).
1. Проверьте компонент **Просмотр событий** на компьютере, а также найдите любые события, которые содержат слово `changetracking`.
1. Дополнительные сведения о том, какие адреса и порты должны быть разрешены для работы Отслеживания изменений, см. в разделе [Настройка сети](../automation-hybrid-runbook-worker.md#network-planning).
1. Убедитесь, что на локальном компьютере существуют следующие пакеты служб "Отслеживание изменений" и "Учет".
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Если вы используете клонированный образ, выполните для него sysprep, а затем установите агент MMA.

Если эти решения не устранят проблему, вы можете обратиться в службу поддержки, выполнив следующие команды для сбора диагностических данных об агенте.

На компьютере агента, перейдите к `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` и выполните следующие команды:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> По умолчанию трассировка ошибок включена. Если вы намерены включить подробные сообщения об ошибках, как в предыдущем примере, используйте параметр `VER`. Для трассировки информации используйте `INF` при вызове `StartTracing.cmd`.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
