---
title: Обзор Azure Arc для серверов (предварительная версия)
description: Узнайте, как использовать Azure Arc для серверов, чтобы управлять компьютерами, размещенными вне Azure, как в облаке Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: служба автоматизации Azure, DSC, PowerShell, настройка требуемого состояния, управление обновлениями, отслеживание изменений, инвентаризация, модули runbook, Python, графический, гибридный
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: b0f1d235391c4c4e3804a6dccc8174e946035b6a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899203"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Что такое Azure Arc для серверов (предварительная версия)

Служба "Azure Arc для серверов" (предварительная версия) позволяет управлять компьютерами Windows и Linux, размещенными за пределами Azure, в корпоративной сети или другом поставщике облачных служб, как если бы они находились в среде Azure. Если компьютер с гибридной рабочей ролью, не относящийся к Azure, подключен к Azure, он становится подключенным компьютером и рассматривается как ресурс в Azure. Каждый подключенный компьютер имеет идентификатор ресурса, управляется как часть группы ресурсов в подписке, а также получает преимущества стандартных функций Azure, таких как Политика Azure и расстановка тегов.

Для обеспечения такого взаимодействия с компьютерами с гибридной рабочей ролью, размещенными вне Azure, агент Azure Connected Machine следует установить на каждом компьютере, который планируется подключить к Azure. Этот агент не предоставляет никакие другие функции и не заменяет [агент Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Этот предварительный выпуск предназначен для оценки, поэтому не рекомендуется управлять с его помощью важными рабочими компьютерами.
>

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

Служба Azure Arc для серверов (предварительная версия) поддерживает следующие сценарии для подключенных компьютеров:

- Назначение [конфигураций гостевых политик Azure](../../governance/policy/concepts/guest-configuration.md) с помощью того же интерфейса, что и для назначения политики для виртуальных машин Azure.
- Данные журнала, собранные агентом Log Analytics и сохраненные в рабочей области Log Analytics, в которой зарегистрирован компьютер, теперь содержат свойства, характерные для этого компьютера,такие как идентификатор ресурса, который можно использовать для реализации поддержки доступа к журналу[resource-context ](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Поддерживаемые регионы

Azure Arc для серверов (предварительная версия) поддерживает только некоторые регионы:

- WestUS2
- WestEurope
- WestAsia

## <a name="prerequisites"></a>Предварительные требования

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Агент Azure Connected Machine официально поддерживают следующие версии операционных систем Windows и Linux: 

- Windows Server 2012 R2 и более поздних версий;
- Ubuntu 16.04 и 18.04.

>[!NOTE]
>Этот предварительный выпуск агента подключенного компьютера для Windows поддерживает только англоязычную версию Windows Server.
>

### <a name="azure-subscription-and-service-limits"></a>Ограничения подписки и служб Azure

Прежде чем настраивать компьютеры с поддержкой Azure Arc для серверов (предварительная версия), необходимо просмотреть [ограничения подписки](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) Azure Resource Manager и [ограничения группы ресурсов](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits), чтобы спланировать число подключенных компьютеров.

### <a name="networking-configuration"></a>Конфигурация сети

Агент Connected Machine для Linux и Windows обменивается исходящими данными со службой Azure Arc через TCP-порт 443. Если компьютер подключен к брандмауэру или прокси-серверу для обмена данными через Интернет, ознакомьтесь с предварительными требованиями ниже, чтобы должным образом настроить сеть.

Если исходящее подключение ограничено брандмауэром или прокси-сервером, убедитесь, что указанные ниже URL-адреса не заблокированы. Если разрешены только диапазоны IP-адресов или доменные имена, необходимые агенту для связи со службой, необходимо также разрешить доступ к следующим тегам и URL-адресам службы.

Теги служб:

- AzureActiveDirectory
- AzureTrafficManager.

URL-адреса:

| Ресурс агента | Описание |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Гостевая конфигурация|
|*-agentservice-prod-1.azure-automation.net|Гостевая конфигурация|
|*.his.hybridcompute.azure-automation.net|Служба гибридной идентификации|

Список IP-адресов для каждого тега или региона службы см. в файле JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Диапазоны IP-адресов и теги служб Azure — общедоступное облако). Корпорация Майкрософт публикует еженедельные обновления, содержащие каждую службу Azure и диапазоны IP-адресов, которые она использует. Дополнительные сведения см. в разделе [Теги служб](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

URL-адреса в предыдущей таблице необходимы в дополнение к сведениям о диапазоне IP-адресов для тега службы, так как в большинстве служб сейчас нет регистрации тега службы. В связи с этим IP-адреса могут изменяться. Если для настройки брандмауэра требуются диапазоны IP-адресов, то для предоставления доступа ко всем службам Azure следует использовать тег службы **AzureCloud**. Не отключайте мониторинг безопасности или проверку URL-адресов, но предоставьте такие же разрешения, как для интернет-трафика.

### <a name="register-azure-resource-providers"></a>Регистрация поставщиков ресурсов Azure

Использование службы Azure Arc для серверов (предварительная версия) зависит от следующих поставщиков ресурсов Azure в подписке:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Если поставщики не зарегистрированы, их можно зарегистрировать с помощью таких команд:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Вы также можете зарегистрировать поставщики ресурсов с помощью портала Azure, выполнив действия, описанные в [этом разделе](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Агент подключенного компьютера

Пакет агента Azure Connected Machine для Windows и Linux можно скачать из указанных ниже расположений.

- [Пакет установщика Windows для агента Windows](https://aka.ms/AzureConnectedMachineAgent) из Центра загрузки Майкрософт.
- Агент Linux распределяется через [репозиторий пакетов](https://packages.microsoft.com/) Microsoft в предпочтительном для распределения пакетном формате (RPM или DEB).

>[!NOTE]
>В предварительной версии выпущен только один пакет, который подходит для Ubuntu 16.04 или 18.04.

## <a name="install-and-configure-agent"></a>Установка и настройка агента

Подключение компьютеров в гибридной среде напрямую к Azure может осуществляться разными способами в зависимости от требований. В следующей таблице описан каждый метод. Эти данные помогут вам определить, какой из методов самый подходящий для вашей организации.

| Метод | Описание |
|--------|-------------|
| Интерактивно | Вручную установите агент на одном или нескольких компьютерах, выполнив действия, описанные в статье [Краткое руководство. Подключение компьютеров к Azure с помощью Azure Arc для серверов на портале](quickstart-onboard-portal.md).<br> На портале Azure можно создать сценарий и выполнить его на компьютере, чтобы автоматизировать шаги установки и настройки агента.|
| В большом масштабе | Установите и настройте агент для нескольких машин, выполнив указания в статье [Краткое руководство. Подключение компьютеров к Azure с помощью Azure Arc для серверов в PowerShell](quickstart-onboard-powershell.md).<br> Этот метод создает субъект-службу для подключения компьютеров в неинтерактивном режиме.|


## <a name="next-steps"></a>Дальнейшие действия

- Чтобы начать оценку Azure Arc для серверов (предварительная версия), следуйте инструкциям в статье [Краткое руководство. Подключение компьютеров к Azure с помощью Azure Arc для серверов на портале](quickstart-onboard-portal.md). 