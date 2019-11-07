---
title: Общие сведения об Azure Arc для серверов
description: Сведения об использовании Azure Arc для серверов для автоматизации жизненного цикла инфраструктуры и приложений.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: служба автоматизации Azure, DSC, PowerShell, настройка требуемого состояния, управление обновлениями, отслеживание изменений, инвентаризация, модули runbook, Python, графический, гибридный
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 0178f27c39c065399208e7fc5afe7352ba4f8e30
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488220"
---
# <a name="what-is-azure-arc-for-servers"></a>Что собой представляет Azure Arc для серверов

Azure Arc для серверов позволяет управлять компьютерами, которые находятся за пределами Azure.
Если виртуальная машина, не относящаяся к Azure, подключена к Azure, она становится **подключенным компьютером** и считается ресурсом в Azure. Каждый **подключенный компьютер** имеет идентификатор ресурса, управляется как часть группы ресурсов в подписке, а также получает преимущества стандартных функций Azure, таких как Политика Azure и расстановка тегов.

Для подключения к Azure пакет агента необходимо установить на каждом компьютере. В оставшейся части этого документа процесс описывается более подробно.

Компьютеры будут находиться в состоянии **Подключено** или **Отключено** в зависимости от того, как давно агент выполнял возврат. Каждый возврат называется пульсом. Если компьютер не выполнял возврат в течение последних 5 минут, он будет отображаться как находящийся вне сети, пока подключение не будет восстановлено.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Подключенные серверы](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Клиенты

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

В общедоступной предварительной версии поддерживается:

- Windows Server 2012 R2 и более поздней версии;
- Ubuntu 16.04 и 18.04.

Выпуск общедоступной предварительной версии предназначен для ознакомления, и его не следует использовать для управления критически важными рабочими ресурсами.

## <a name="azure-subscription-and-service-limits"></a>Ограничения подписки и службы Azure

Обязательно ознакомьтесь с ограничениями Azure Resource Manager и запланируйте количество компьютеров, которые необходимо подключить, в соответствии с рекомендациями, указанными для [подписки](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), а также для [групп ресурсов](../../azure-subscription-service-limits.md#resource-group-limits). В частности, на группу ресурсов по умолчанию установлено ограничение в 800 серверов.

## <a name="networking-configuration"></a>Конфигурация сети

Во время установки и выполнения агенту требуется подключение к **конечным точкам службы Azure Arc**. При блокировке исходящего подключения с помощью брандмауэров убедитесь, что в список запрещенных не были по умолчанию добавлены указанные ниже URL-адреса. Все подключения исходят от агента в Azure и защищены с помощью **SSL**. Весь трафик может маршрутизироваться через прокси-сервер **HTTPS**. Если разрешаете IP-диапазоны или доменные имена, к которым разрешено подключаться серверам, необходимо разрешить доступ через порт 443 к следующим тегам служб и DNS-именам.

Теги служб:

* AzureActiveDirectory
* AzureTrafficManager.

Список IP-адресов для каждого тега или региона службы см. в файле JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Диапазоны IP-адресов и теги служб Azure — общедоступное облако). Корпорация Майкрософт публикует еженедельные обновления, содержащие каждую службу Azure и диапазоны IP-адресов, которые она использует. Дополнительные сведения см. в разделе о [тегах служб](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Эти DNS-имена предоставляются в дополнение к сведениям о диапазоне IP-адресов для тега службы, так как в большинстве служб сейчас нет регистрации тега службы, в связи с чем IP-адреса могут изменяться. Если для настройки брандмауэра требуются диапазоны IP-адресов, то для предоставления доступа ко всем службам Azure следует использовать тег службы **AzureCloud**. Не отключайте мониторинг безопасности или проверку URL-адресов, но предоставьте им разрешения такие же, как и интернет-трафику.

| Доменная среда | Требуемые конечные точки службы Azure |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Гостевая конфигурация|
|*-agentservice-prod-1.azure-automation.net|Гостевая конфигурация|
|*.his.hybridcompute.azure-automation.net|Служба гибридной идентификации|

### <a name="installation-network-requirements"></a>Требования сети к установке

Скачайте [пакет агента подключенного компьютера Azure](https://aka.ms/AzureConnectedMachineAgent) с официальных серверов распространения. Указанные ниже сайты должны быть доступны из вашей среды. Вы можете скачать пакет в общую папку и установить агент из нее. В этом случае может потребоваться изменить скрипт подключения, созданный на портале Azure.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Сведения о настройке агента для использования прокси-сервера см. в разделе о [конфигурации прокси-сервера](quickstart-onboard-powershell.md#proxy-server-configuration).

## <a name="register-the-required-resource-providers"></a>Регистрация необходимых поставщиков ресурсов

После утверждения регистрации флага компонента необходимо зарегистрировать необходимые поставщики ресурсов.

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Ниже приведены команды, с помощью которых можно зарегистрировать поставщики ресурсов.

Azure PowerShell

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Вы также можете зарегистрировать поставщики ресурсов с помощью портала Azure, выполнив действия, описанные в [этом разделе](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

После регистрации узла можно приступить к управлению узлами с помощью других служб Azure.

В общедоступной предварительной версии поддерживаются следующие сценарии для **подключенных компьютеров**.

## <a name="guest-configuration"></a>Гостевая конфигурация

После подключения компьютера к Azure вы можете назначить политики Azure **подключенным компьютерам**, используя те же действия, что и для назначения политик для виртуальных машин Azure.

Дополнительные сведения см.в статье [Understand Azure Policy's Guest Configuration](../../governance/policy/concepts/guest-configuration.md) (Общие сведения о гостевой конфигурации службы "Политика Azure").

Журналы агента гостевой конфигурации для **подключенного компьютера** находятся в следующих расположениях:

* Для Windows — `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`.
* Для Linux — `/opt/logs/dsc.log`.

## <a name="log-analytics"></a>Log Analytics

Данные журнала, собранные [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) и хранящиеся в рабочей области Log Analytics, теперь содержат свойства, относящиеся к компьютеру, такие как **ResourceId**, которые можно использовать для доступа к журналам ресурсов.

- На компьютерах, на которых уже установлен агент MMA, будут доступны функции **Azure Arc**, включенные с помощью обновленных пакетов управления.
- Для интеграции Azure Arc для серверов требуется [агент MMA версии 10.20.18011 или более поздней](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version).
- При запросе данных журнала в [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#log-queries) возвращаемая схема данных будет содержать гибридный **ResourceId** в форме `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Дополнительные сведения см. в статье [Начало работы с Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Дальнейшие действия

Подключить компьютеры с помощью Azure Arc для серверов можно двумя способами.

* **Интерактивно.** Следуйте инструкциям в [кратком руководстве по порталу](quickstart-onboard-portal.md), чтобы создать скрипт на портале и выполнить его на компьютере. Это лучший вариант, если вы подключаете один компьютер за раз.
* **В масштабе.** Следуйте инструкциям в [кратком руководстве по PowerShell](quickstart-onboard-powershell.md), чтобы создать субъект-службу для подключения компьютеров в неинтерактивном режиме.
