---
title: Управление расширениями виртуальных машин с помощью серверов с поддержкой дуги Azure
description: Серверы с поддержкой Arc Azure могут управлять развертыванием расширений виртуальных машин, которые обеспечивают настройку и задачи автоматизации после развертывания с помощью виртуальных машин, не относящихся к Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460892"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Управление расширениями виртуальных машин с помощью серверов с поддержкой дуги Azure

Расширения виртуальных машин — это небольшие приложения, которые обеспечивают настройку и задачи автоматизации, выполняемые после развертывания на виртуальных машинах Azure. Например, если на виртуальную машину требуется установить программное обеспечение, антивирусную защиту или выполнить сценарий внутри него, можно использовать расширение виртуальной машины.

Серверы с поддержкой Arc Azure позволяют развертывать расширения виртуальных машин Azure на виртуальных машинах Windows и Linux без Azure, упрощая управление гибридными компьютерами в локальной среде, пограничных и других облачных средах с помощью их жизненного цикла. Для управления расширениями виртуальной машины можно использовать следующие методы на гибридных компьютерах или серверах, управляемых с помощью серверов с поддержкой ARC:

- [портал Azure](manage-vm-extensions-portal.md).
- [Интерфейс командной строки Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Шаблоны диспетчер ресурсов](manage-vm-extensions-template.md) Azure

## <a name="key-benefits"></a>Основные преимущества

Поддержка расширения виртуальной машины на серверах с поддержкой дуги Azure обеспечивает следующие основные преимущества.

- Используйте [конфигурацию состояния службы автоматизации Azure](../../automation/automation-dsc-overview.md) для централизованного хранения конфигураций и поддержки требуемого состояния гибридных подключенных компьютеров, включенных с помощью расширения ВИРТУАЛЬНОЙ машины DSC.

- Собирайте данные журналов для анализа с помощью [журналов в Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) , включенных с помощью расширения виртуальной машины агента log Analytics. Это полезно для выполнения сложного анализа данных из различных источников.

- С помощью [Azure Monitor для виртуальных машин](../../azure-monitor/insights/vminsights-overview.md)анализирует производительность виртуальных машин Windows и Linux и отслеживает их процессы и зависимости от других ресурсов и внешних процессов. Это достигается благодаря включению как агента Log Analytics, так и расширений виртуальной машины агента зависимостей.

- Скачайте и выполните скрипты на гибридных подключенных компьютерах с помощью расширения пользовательских сценариев. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления.

## <a name="availability"></a>Доступность

Функциональность расширения виртуальной машины доступна только в списке [поддерживаемых регионов](overview.md#supported-regions). Убедитесь, что ваш компьютер подключен в одном из этих регионов.

## <a name="extensions"></a>Модули

В этом выпуске поддерживаются следующие расширения виртуальных машин на компьютерах Windows и Linux.

|Расширение |ОС |Publisher |Дополнительные сведения |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute; |[Расширение пользовательских сценариев Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Расширение DSC Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Агент Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. extension |[Расширение пользовательских сценариев Linux версии 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Расширение PowerShell DSC для Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Агент Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Сведения о пакете агента подключенного компьютера Azure и сведения о компоненте агента расширения см. в разделе [Обзор агентов](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Предварительные требования

Эта функция зависит от следующих поставщиков ресурсов Azure в вашей подписке:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Если они еще не зарегистрированы, выполните действия, описанные в разделе [Регистрация поставщиков ресурсов Azure](agent-overview.md#register-azure-resource-providers).

Для расширения виртуальной машины агента Log Analytics для Linux требуется, чтобы на целевом компьютере был установлен Python 2. x.

### <a name="connected-machine-agent"></a>Агент подключенного компьютера

Убедитесь, что компьютер соответствует [поддерживаемым версиям](agent-overview.md#supported-operating-systems) операционной системы Windows и Linux для агента подключенного компьютера Azure.

Минимальная версия агента подключенного компьютера, поддерживаемая с этой функцией в Windows и Linux, — это выпуск 1,0.

Сведения об обновлении компьютера до версии, необходимой для агента, см. в разделе [Upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Дальнейшие действия

Расширения виртуальных машин можно развертывать, администрировать и удалять с помощью [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md), из [портал Azure](manage-vm-extensions-portal.md)или [шаблонов Azure Resource Manager](manage-vm-extensions-template.md).
