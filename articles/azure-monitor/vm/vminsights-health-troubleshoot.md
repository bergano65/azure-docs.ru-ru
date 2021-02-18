---
title: Устранение неполадок работоспособности гостевых виртуальных машин в Azure Monitor (предварительная версия)
description: Описание действий по устранению неполадок, которые можно выполнить при возникновении проблем с Azure Monitor для виртуальных машин работоспособностью.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: da8097341f8499be4e28fa37c06d963d057966ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622173"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Устранение неполадок работоспособности гостевых виртуальных машин в Azure Monitor (предварительная версия)
В этой статье описаны действия по устранению неполадок, которые можно выполнить при возникновении проблем с Azure Monitor для виртуальных машин работоспособностью.

## <a name="error-message-that-no-data-is-available"></a>Сообщение об ошибке, в котором нет доступных данных 

![Нет данных](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Убедитесь, что виртуальная машина соответствует требованиям к конфигурации.

1. Убедитесь, что виртуальная машина является виртуальной машиной Azure. Служба "Azure Arc для серверов" сейчас не поддерживается.
2. Убедитесь, что на виртуальной машине работает [поддерживаемая версия операционной системы](vminsights-health-enable.md?current-limitations.md).
3. Убедитесь, что виртуальная машина установлена в [поддерживаемом регионе](vminsights-health-enable.md?current-limitations.md).
4. Убедитесь, что рабочая область Log Analytics установлена в [поддерживаемом регионе](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Убедитесь, что виртуальная машина правильно подключена
Убедитесь, что расширение агента Azure Monitor и агент работоспособности гостевой виртуальной машины успешно подготовлены на виртуальной машине. Выберите **расширения** в меню виртуальной машины в портал Azure и убедитесь, что указаны два агента.

![Расширения виртуальных машин](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Убедитесь, что на виртуальной машине включено удостоверение, назначенное системой.
Убедитесь, что на виртуальной машине включено удостоверение, назначенное системой. Выберите **удостоверение** из меню виртуальной машины в портал Azure. 

![Системное удостоверение](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Проверка правила сбора данных
Убедитесь, что правило сбора данных, задающее расширение работоспособности в качестве источника данных, связано с виртуальной машиной.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Сообщение об ошибке для неправильного запроса из-за недостаточных разрешений
Эта ошибка означает, что поставщик ресурсов **Microsoft. WorkloadMonitor** не был зарегистрирован в подписке. Дополнительные сведения о регистрации этого поставщика ресурсов см. в статье [поставщики и типы ресурсов Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Недопустимый запрос](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Дальнейшие шаги

- [Ознакомьтесь с обзором функции работоспособности гостевой системы Azure Monitor для виртуальных машин](vminsights-health-overview.md)