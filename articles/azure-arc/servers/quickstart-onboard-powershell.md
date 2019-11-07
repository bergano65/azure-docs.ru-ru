---
title: 'Краткое руководство: подключение компьютеров к Azure с помощью Azure Arc для серверов — PowerShell'
description: В этом кратком руководстве вы узнаете, как подключить компьютеры к Azure с помощью Azure Arc для серверов с использованием PowerShell
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: служба автоматизации Azure, DSC, PowerShell, настройка требуемого состояния, управление обновлениями, отслеживание изменений, инвентаризация, модули runbook, Python, графический, гибридный, подключение
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 91d8ddf7d8051baeb42ceb58673c93555908f03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488180"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Краткое руководство. Подключение компьютеров к Azure с помощью Azure Arc для серверов в PowerShell

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Ознакомьтесь со сведениями о поддерживаемых клиентах и требуемой конфигурации сети в [обзоре решения Azure Arc для серверов](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Создание субъекта-службы для масштабного подключения

Субъект-служба — это специальное ограниченное удостоверение управления, которому предоставляются только минимальные разрешения, необходимые для подключения компьютеров к Azure. Это более безопасно, чем использовать учетную запись с большим количеством разрешений, например учетную запись администратора клиента. Субъект-служба используется только во время подключения. Субъект-службу можно безопасно удалить после подключения к нужным серверам.

> [!NOTE]
> Этот шаг рекомендуется, но не является обязательным.

### <a name="steps-to-create-the-service-principal"></a>Действия по созданию субъекта-службы

В этом примере для создания имени субъекта-службы (SPN) будет использоваться [Azure PowerShell](/powershell/azure/install-az-ps). Кроме того, для этой задачи можно выполнить действия, описанные в статье [Практическое руководство. Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md).

Роль `Azure Connected Machine Onboarding` содержит только разрешения, необходимые для подключения. Вы можете определить разрешение имени субъекта-службы, чтобы расширить область его охвата до группы ресурсов или подписки.

Необходимо сохранить выходные данные командлета [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal), иначе вы не сможете получить пароль, который будет использоваться в дальнейшем.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Теперь получите пароль с помощью PowerShell.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Из выходных данных скопируйте **пароль** и **ApplicationId** (из предыдущего шага) и сохраните их для последующего использования в надежном месте, например в хранилище секретов для средства настройки сервера. Если вы забудете или потеряете пароль имени субъекта-службы, его можно сбросить с помощью командлета [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

В сценарии подключения агента установки сделайте следующее:

* Свойство **ApplicationId** используется для параметра `--service-principal-id` в агенте установки.
* Свойство **password** используется для параметра `--service-principal-secret` в агенте установки.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Установка агента вручную и подключение к Azure

С помощью приведенных ниже инструкций можно подключить компьютер к Azure, войдя на компьютер и выполнив соответствующие шаги. Подключить компьютеры к Azure можно также [на портале](quickstart-onboard-portal.md).

### <a name="download-and-install-the-agent"></a>Загрузка и установка агента

Для установки пакета агента требуются права доступа привилегированного пользователя или локального администратора на целевом сервере, но не доступ к Azure.

#### <a name="linux"></a>Linux

Для серверов **Linux** агент распределяется через [репозиторий пакетов Майкрософт](https://packages.microsoft.com) с использованием предпочтительного формата пакетов для дистрибутива (RPM или DEB).

> [!NOTE]
> В общедоступной предварительной версии выпущен только один пакет, который подходит для Ubuntu 16.04 или 18.04.

<!-- What about this aks? -->
Самый простой вариант — зарегистрировать репозиторий пакетов, а затем установить пакет с помощью диспетчера пакетов дистрибутива.
Этот bash-скрипт, расположенный в [https://aka.ms/azcmagent](https://aka.ms/azcmagent), выполняет следующие действия:

1. Настраивает на хост-компьютере скачивание с сайта `packages.microsoft.com`.
2. Устанавливает пакет поставщика гибридных ресурсов.
3. При необходимости настраивает агент для операции прокси-сервера, если указать `--proxy`.

Скрипт также содержит проверки на наличие поддерживаемых и неподдерживаемых дистрибутивов, а также код обнаружения необходимых разрешений для установки.

В приведенном ниже примере агент скачивается и устанавливается без каких-либо условных проверок.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Если вы предпочитаете не ссылаться на репозиторий пакетов Майкрософт, можно скопировать файл пакета из него во внутренний репозиторий.

#### <a name="windows"></a>Windows

Для **Windows** агент упаковывается в файл установщика Windows (`.MSI`). Его можно скачать из [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), который размещается на сайте [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> Если в Linux повторно выполнить скрипт установки, автоматически выполнится обновление до последней версии. В Windows агент подключенного компьютера Azure необходимо удалить, прежде чем повторно запускать установщик для обновления.

### <a name="connecting-to-azure"></a>Подключение к Azure

После установки агентом можно управлять и настраивать его с помощью программы командной строки с именем `azcmagent.exe`. Агент находится в папке `/opt/azcmagent/bin` в Linux и `$env:programfiles\AzureConnectedMachineAgent` в Windows.

В Windows откройте PowerShell от имени администратора на целевом узле и выполните команду:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

В Linux откройте оболочку и выполните команду:

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Параметры

* `tenant-id` : Уникальный идентификатор клиента. Его можно найти на портале Azure, выбрав **Azure Active Directory** -> **Свойства** -> **Идентификатор каталога**.
* `subscription-id` : Уникальный идентификатор подписки в Azure, в которой необходимо подключить компьютер.
* `resource-group` : Группа ресурсов, в которой необходимо подключить компьютер.
* `location` : См. сведения о [регионах и расположениях Azure](https://azure.microsoft.com/global-infrastructure/regions/). Это расположение может совпадать или не совпадать с расположением группы ресурсов. Для общедоступной предварительной версии служба поддерживается в **западной части США 2** и **Западной Европе**.
* `resource-name` :  (*Необязательно*.) Используется для представления ресурсов Azure на локальном компьютере. Если это значение не указано, будет использоваться имя узла компьютера.

Дополнительные сведения о средстве azcmagent см. в справочнике по [azcmagent](azcmagent-reference.md).
<!-- Isn't this still needed to view machines? -->

После успешного завершения ваш компьютер будет подключен к Azure. Состояние компьютера можно просмотреть на портале Azure, посетив страницу [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Успешное подключение](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Конфигурация прокси-сервера

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

Если в **Linux** серверу требуется прокси-сервер, можно выполнить одно из следующих действий.

* Запустите скрипт `install_linux_hybrid_agent.sh`, работая с разделом об [установке агента](#download-and-install-the-agent) выше, с использованием `--proxy`.
* Если агент уже установлен, выполните команду `/opt/azcmagent/bin/hybridrp_proxy add https://{proxy-url}:{proxy-port}`, которая настраивает прокси-сервер и перезапускает агент.

#### <a name="windows"></a>Windows

Если в **Windows** серверу требуется прокси-сервер для доступа к ресурсам в Интернете, следует выполнить приведенную ниже команду, чтобы задать переменную среды прокси-сервера. Это позволяет агенту использовать прокси-сервер для доступа к Интернету.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "{https:\\proxy-url:proxyport}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Прошедшие проверку подлинности прокси-серверы не поддерживаются в общедоступной предварительной версии.

## <a name="clean-up"></a>Очистка

Чтобы отключить компьютер от Azure Arc для серверов, необходимо выполнить два действия:

1. Выберите компьютер на [портале](https://aka.ms/hybridmachineportal), щелкните многоточие (`...`) и выберите команду **Удалить**.
1. Удалите агент с компьютера.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Назначение политики подключенным компьютерам](../../governance/policy/assign-policy-portal.md)
