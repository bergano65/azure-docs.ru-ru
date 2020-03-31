---
title: Подключение гибридных компьютеров к Azure на портале Azure
description: Из этой статьи вы узнаете, как установить агент и подключить компьютеры к Azure с помощью Azure Arc для серверов (предварительная версия) на портале Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366364"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Подключение гибридных компьютеров к Azure на портале Azure

Вы можете включить Azure Arc для серверов (предварительная версия) для одного или нескольких компьютеров Windows или Linux в вашей среде, выполнив несколько этапов вручную. Вы также можете использовать автоматизированный метод — запустить скрипт шаблона, который мы предоставляем. Этот скрипт автоматизирует скачивание и установку обоих агентов.

Этот метод требует наличия разрешений администратора на компьютере для установки и настройки агента. В Linux, используя корневую учетную запись, и в Windows вы являетесь членом группы локальных администраторов.

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](overview.md#prerequisites) и убедитесь, что подписка и ресурсы соответствуют требованиям.

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
    >Просмотрите дополнительные соображения при выборе региона [здесь,](overview.md#supported-regions) в статье Обзор.

1. На странице **Создать скрипт** в раскрывающемся списке **Операционная система** выберите операционную систему, для которой будет выполняться скрипт.

1. Если машина общается через прокси-сервер для подключения к Интернету, выберите **Next: Proxy Server.** 
1. На вкладке **Прокси-сервер** укажите IP-адрес прокси-сервера или имя и номер порта, которые будет использовать компьютер для связи с прокси-сервером. Введите значение в формате `http://<proxyURL>:<proxyport>`. 
1. Выберите вкладку **Проверка и создание**.

1. На вкладке **Проверка и создание** просмотрите сводные данные, а затем щелкните **Загрузка**. Если вам все еще нужно внести изменения, щелкните **Назад**.

## <a name="install-and-validate-the-agent-on-windows"></a>Установка и проверка агента в ОС Windows

### <a name="install-manually"></a>Установка вручную

Агент подключенного компьютера можно установить вручную, запустив пакет установщика Windows *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Чтобы установить или удалить агент, необходимы разрешения *Администратора*.
> * Сначала необходимо скачать и скопировать пакет установщика в папку на целевом сервере или сделать это из общей сетевой папки. При запуске пакета установщика без настраивания каких-либо параметров активируется мастер установки, который можно использовать для установки агента в интерактивном режиме.

Если компьютеру необходимо взаимодействовать через прокси-сервер со службой, после установки агента необходимо выполнить команду, описанную далее в этой статье. Она задает переменную системной среды для прокси-сервера `https_proxy`.

Если вы не знакомы с вариантами командной строки для пакетов Windows Installer, просмотрите [стандартные параметры командной строки Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) и [параметры командной строки Msiexec.](https://docs.microsoft.com/windows/win32/msi/command-line-options)

Например, запустите программу `/?` установки с параметром для просмотра справки и опции быстрого отсчета. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Чтобы установить агент бесшумно и создать `C:\Support\Logs` файл журнала настройки в папке, запустите следующую команду.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Файлы для агента Connected Machine устанавливаются по умолчанию в *C: «Файлы программы»AzureConnectedMachineAgent*. Если не удается запустить агент после завершения установки, просмотрите подробные сведения об ошибке в журналах. Каталог журнала — *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Установка с помощью метода скрипта

1. Войдите на сервер.

1. Откройте командную строку PowerShell с повышенными привилегиями.

1. Перейдите к обычной или общей папке, в которую скопировали скрипт, и выполните его на сервере, запустив `./OnboardingScript.ps1`.

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

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

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

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Проверка подключения с помощью Azure Arc

После установки агента и настройки его подключения к Azure Arc для серверов (предварительная версия) перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://aka.ms/hybridmachineportal).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять машиной с помощью [azure Policy,](../../governance/policy/overview.md)для таких вещей, как [конфигурация гостевых VM,](../../governance/policy/concepts/guest-configuration.md)проверка того, что машина отчитывается перед ожидаемым рабочим пространством Log Analytics, позволяет осуществлять мониторинг с помощью [Azure Monitor с помощью vMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)и многое другое.

- Подробнее об [агенте Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).
