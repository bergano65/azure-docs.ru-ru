---
title: Управление расширениями виртуальных машин с помощью серверов с поддержкой дуги Azure
description: Серверы с поддержкой Arc Azure могут управлять развертыванием расширений виртуальных машин, которые обеспечивают настройку и задачи автоматизации после развертывания с помощью виртуальных машин, не относящихся к Azure.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 7682f6c8631bbaf2310d501d7cee6aecb2311226
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358037"
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

- Автоматическое обновление сертификатов, хранящихся в [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Доступность

Функциональность расширения виртуальной машины доступна только в списке [поддерживаемых регионов](overview.md#supported-regions). Убедитесь, что ваш компьютер подключен в одном из этих регионов.

## <a name="extensions"></a>Расширения

В этом выпуске поддерживаются следующие расширения виртуальных машин на компьютерах Windows и Linux.

|Расширение |ОС |Publisher |Дополнительные сведения |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute; |[Расширение пользовательских сценариев Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Расширение DSC Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Агент Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute; | [Расширение виртуальной машины Key Vault для Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. extension |[Расширение пользовательских сценариев Linux версии 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Расширение PowerShell DSC для Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Агент Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute; | [Расширение виртуальной машины Key Vault для Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Сведения о пакете агента подключенного компьютера Azure и сведения о компоненте агента расширения см. в разделе [Обзор агентов](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Предварительные требования

Эта функция зависит от следующих поставщиков ресурсов Azure в вашей подписке:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Если они еще не зарегистрированы, выполните действия, описанные в разделе [Регистрация поставщиков ресурсов Azure](agent-overview.md#register-azure-resource-providers).

### <a name="log-analytics-vm-extension"></a>Расширение виртуальной машины Log Analytics

Для расширения виртуальной машины агента Log Analytics для Linux требуется, чтобы на целевом компьютере был установлен Python 2. x.

### <a name="azure-key-vault-vm-extension-preview"></a>Расширение виртуальной машины Azure Key Vault (Предварительная версия)

Расширение Key Vault VM (Предварительная версия) не поддерживает следующие операционные системы Linux:

- CentOS Linux 7 (x64);
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Развертывание расширения Key Vault VM (Предварительная версия) поддерживается только с помощью:

- Azure CLI
- Azure PowerShell
- Шаблон Azure Resource Manager

Перед развертыванием расширения необходимо выполнить следующие действия.

1. [Создайте хранилище и сертификат](../../key-vault/certificates/quick-create-portal.md) (самозаверяющий или импортированный).

2. Предоставьте серверу с поддержкой Arc Azure доступ к секрету сертификата. Если вы используете [предварительную версию RBAC](../../key-vault/general/rbac-guide.md), найдите имя ресурса Arc Azure и назначьте его роли **пользователя Key Vault секреты (Предварительная версия)** . Если вы используете [политику доступа Key Vault](../../key-vault/general/assign-access-policy-portal.md), назначьте ей разрешения на **Получение** секрета для назначенного системе удостоверения ресурса дуги Azure.

### <a name="connected-machine-agent"></a>Агент подключенного компьютера

Убедитесь, что компьютер соответствует [поддерживаемым версиям](agent-overview.md#supported-operating-systems) операционной системы Windows и Linux для агента подключенного компьютера Azure.

Минимальная версия агента подключенного компьютера, поддерживаемая с этой функцией в Windows и Linux, — это выпуск 1,0.

Сведения об обновлении компьютера до версии, необходимой для агента, см. в разделе [Upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Дальнейшие шаги

Расширения виртуальных машин можно развертывать, администрировать и удалять с помощью [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), из [портал Azure](manage-vm-extensions-portal.md)или [шаблонов Azure Resource Manager](manage-vm-extensions-template.md).
