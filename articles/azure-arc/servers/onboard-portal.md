---
title: Подключение гибридных компьютеров к Azure на портале Azure
description: Из этой статьи вы узнаете, как установить агент и подключить компьютеры к Azure с помощью Azure Arc для серверов (предварительная версия) на портале Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 52c53cc10fe6517be6083a14c98daa9e6ff3b56f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648085"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Подключение гибридных компьютеров к Azure на портале Azure

Вы можете включить Azure Arc для серверов (предварительная версия) для одного или нескольких компьютеров Windows или Linux в вашей среде, выполнив несколько этапов вручную. Вы также можете использовать автоматизированный метод — запустить скрипт шаблона, который мы предоставляем. Этот скрипт автоматизирует скачивание и установку обоих агентов.

Этот метод требует наличия разрешений администратора на компьютере для установки и настройки агента. В Linux, используя корневую учетную запись, и в Windows вы являетесь членом группы локальных администраторов.

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](agent-overview.md#prerequisites) и убедитесь, что подписка и ресурсы соответствуют требованиям.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Создание скрипта установки на портале Azure

Скрипт для автоматизации скачивания и установки, а также для установки подключения к Azure Arc, доступен на портале Azure. Чтобы завершить процесс, выполните следующие действия:

1. В браузере перейдите на [портал Azure](https://aka.ms/hybridmachineportal).

1. На странице **Компьютеры — Azure Arc** щелкните **Добавить** в левом верхнем углу или **Create machine - Azure Arc** (Создать компьютер — Azure Arc) в нижней части средней области. 

1. На странице **Select a method** (Выбрать метод) выберите плитку **Добавление компьютеров с помощью интерактивного сценария**, а затем щелкните **Создать скрипт**.

1. На странице **Создать скрипт** выберите подписку и группу ресурсов, которые следует использовать для управления компьютером в Azure. Выберите расположение Azure, в котором будут храниться метаданные компьютера.

    >[!NOTE]
    >Azure Arc для серверов (предварительная версия) поддерживает только следующие регионы:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >При [выборе региона](overview.md#supported-regions) ознакомьтесь с дополнительными замечаниями в обзорной статье.

1. На странице **Создать скрипт** в раскрывающемся списке **Операционная система** выберите операционную систему, для которой будет выполняться скрипт.

1. Если компьютер обменивается данными через прокси-сервер для подключения к Интернету, выберите **Next: Proxy Server** (Далее: Прокси-сервер). 
1. На вкладке **Прокси-сервер** укажите IP-адрес прокси-сервера или имя и номер порта, которые будет использовать компьютер для связи с прокси-сервером. Введите значение в формате `http://<proxyURL>:<proxyport>`. 
1. Выберите вкладку **Проверка и создание**.

1. На вкладке **Проверка и создание** просмотрите сводные данные, а затем щелкните **Загрузка**. Если вам все еще нужно внести изменения, щелкните **Назад**.

## <a name="install-and-validate-the-agent-on-windows"></a>Установка и проверка агента в ОС Windows

### <a name="install-manually"></a>Установка вручную

Агент подключенного компьютера можно установить вручную, запустив пакет установщика Windows *AzureConnectedMachineAgent.msi*. Последнюю версию [пакета установщика Windows для агента Windows](https://aka.ms/AzureConnectedMachineAgent) можно скачать из Центра загрузки Майкрософт. 

> [!NOTE]
> * Чтобы установить или удалить агент, необходимы разрешения *Администратора*.
> * Сначала необходимо скачать и скопировать пакет установщика в папку на целевом сервере или сделать это из общей сетевой папки. При запуске пакета установщика без настраивания каких-либо параметров активируется мастер установки, который можно использовать для установки агента в интерактивном режиме.

Если компьютеру необходимо взаимодействовать через прокси-сервер со службой, после установки агента необходимо выполнить команду, описанную далее в этой статье. Она задает переменную системной среды для прокси-сервера `https_proxy`.

Если вы не работали с параметрами командной строки для пакетов установщика Windows, ознакомьтесь со [стандартными параметрами командной строки Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) и [параметрами командной строки Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Например, запустите программу установки с параметром `/?`, чтобы ознакомиться с параметром справки и краткого справочника. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Чтобы автоматически установить агент и создать файл журнала установки в существующей папке `C:\Support\Logs`, выполните следующую команду.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Если не удается запустить агент после завершения установки, просмотрите подробные сведения об ошибке в журналах. Каталог журнала — *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Установка с помощью метода скрипта

1. Войдите на сервер.

1. Откройте командную строку PowerShell с повышенными привилегиями.

1. Перейдите к обычной или общей папке, в которую скопировали скрипт, и выполните его на сервере, запустив `./OnboardingScript.ps1`.

Если не удается запустить агент после завершения установки, просмотрите подробные сведения об ошибке в журналах. Каталог журнала — *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="configure-the-agent-proxy-setting"></a>Настройка прокси-агента

Чтобы задать переменную среды прокси-сервера, выполните следующую команду:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Агент не поддерживает настройку проверки подлинности прокси-сервера в этой предварительной версии.
>

### <a name="configure-agent-communication"></a>Настройка связи с агентом

После установки агента необходимо настроить для него взаимодействие со службой Azure Arc, выполнив следующую команду:

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Установка и проверка агента в Linux

Агент подключенного компьютера для ОС Linux предоставляется в пакете в предпочтительном формате для распространения (RPM или DEB). Он размещен в [репозитории пакетов](https://packages.microsoft.com/) Microsoft. [Пакет скриптов оболочки `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) выполняет следующие действия:

- настраивает на хост-компьютере скачивание пакета агента по адресу packages.microsoft.com.
- Устанавливает пакет поставщика гибридных ресурсов.

При необходимости можно указать в агенте сведения о прокси-сервере, включив параметр `--proxy "{proxy-url}:{proxy-port}"`.

Скрипт также содержит логику обнаружения поддерживаемых и неподдерживаемых распространений и проверяет разрешения, необходимые для установки. 

В следующем примере выполняется скачивание агента и его установка:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Чтобы скачать и установить агент и включить параметр `--proxy` для настройки взаимодействия агента через прокси-сервер, выполните следующие команды:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Настройка связи с агентом

После установки агента настройте в нем связь со службой Azure Arc, выполнив следующую команду:

`azcmagent connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Проверка подключения с помощью Azure Arc

После установки агента и настройки его подключения к Azure Arc для серверов (предварительная версия) перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://aka.ms/hybridmachineportal).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять компьютером с помощью [политики Azure](../../governance/policy/overview.md), например для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) и т. д.

- Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).
