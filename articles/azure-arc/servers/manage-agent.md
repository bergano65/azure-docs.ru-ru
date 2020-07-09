---
title: Управление агентом Azure Arc для серверов (предварительная версия)
description: В этой статье описаны разные задачи управления, которые обычно можно выполнять в течение жизненного цикла агента Connected Machine в Azure Arc для серверов.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 086491484592c8015bdb519bb498e73da3836f6f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103914"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Обслуживание агента Connected Machine и управление им

После первоначального развертывания агента Connected Machine для Windows или Linux в Azure Arc для серверов (предварительная версия) вам может понадобиться перенастроить его, обновить или удалить из компьютера после завершения жизненного цикла (стадия прекращения применения и списания). Вы можете легко управлять этими задачами обслуживания вручную или автоматизировать этот процесс, сократив при этом эксплуатационные расходы и количество ошибок.

## <a name="upgrading-agent"></a>Обновление агента

Агент Azure Connected Machine для Windows и Linux можно обновить до последней версии вручную или автоматически в зависимости от требований. Следующая таблица описывает методы, поддерживаемые при обновлении агента.

| Операционная система | Метод перехода |
|------------------|----------------|
| Windows | Вручную<br> Центр обновления Windows |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Агент Windows

Пакет обновления для агента Connected Machine для Windows доступен здесь:

* Центр обновления Майкрософт

* [Каталог Центра обновления Майкрософт](https://www.catalog.update.microsoft.com/Home.aspx)

* [Пакет установщика Windows для агента Windows](https://aka.ms/AzureConnectedMachineAgent) из Центра загрузки Майкрософт.

Агент можно обновить, следуя различным методам для поддержки процесса управления обновления программного обеспечения. Помимо получения из Центра обновления Майкрософт, агент можно скачать и запустить вручную из командной строки, из скрипта или другого решения по автоматизации, или из мастера пользовательского интерфейса, выполнив команду `AzureConnectedMachine.msi`.

> [!NOTE]
> * Чтобы обновить агент, необходимы разрешения *Администратора*.
> * Чтобы обновить вручную, сначала необходимо скачать и скопировать пакет установщика в папку на целевом сервере или сделать это из общей сетевой папки. 

Если вы не работали с параметрами командной строки для пакетов установщика Windows, ознакомьтесь со [стандартными параметрами командной строки Msiexec](/windows/win32/msi/standard-installer-command-line-options) и [параметрами командной строки Msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Обновление с помощью мастера установки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Выполните команду **AzureConnectedMachineAgent.msi**, чтобы запустить мастер установки.

Мастер установки определяет наличие предыдущей версии, а затем автоматически обновляет агента. После завершения обновления мастер установки автоматически закроется.

#### <a name="to-upgrade-from-the-command-line"></a>Обновление с помощью командной строки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Чтобы автоматически обновить агент и создать файл журнала установки в папке `C:\Support\Logs`, выполните следующую команду.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Агент Linux

Чтобы обновить агент на компьютере Linux до последней версии, требуется две команды. Одна команда для обновления индекса локального пакета со списком последних доступных пакетов из репозиториев и вторая команда для обновления локального пакета.

Вы можете скачать последнюю версию пакета агента из [репозитория пакетов](https://packages.microsoft.com/) корпорации Майкрософт.

> [!NOTE]
> Чтобы обновить агент, необходимо иметь разрешения *корневого* доступа или учетную запись, которой повысили права с помощью команды Sudo.

#### <a name="upgrade-ubuntu"></a>Обновление Ubuntu

1. Чтобы обновить индекс локального пакета с помощью последних изменений, внесенных в репозитории, выполните следующую команду:

    ```bash
    apt update
    ```

2. Чтобы обновить систему, выполните следующую команду:

    ```bash
    apt upgrade
    ```

Действия команды [apt](https://help.ubuntu.com/lts/serverguide/apt.html), такие как установка и удаление пакетов, записываются в файл журнала `/var/log/dpkg.log`.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Обновление Red Hat/CentOS/Amazon Linux

1. Чтобы обновить индекс локального пакета с помощью последних изменений, внесенных в репозитории, выполните следующую команду:

    ```bash
    yum check-update
    ```

2. Чтобы обновить систему, выполните следующую команду:

    ```bash
    yum update
    ```

Действия команды [yum](https://access.redhat.com/articles/yum-cheat-sheet), такие как установка и удаление пакетов, записываются в файл журнала `/var/log/yum.log`. 

#### <a name="upgrade-suse-linux-enterprise"></a>Обновление SUSE Linux Enterprise

1. Чтобы обновить индекс локального пакета с помощью последних изменений, внесенных в репозитории, выполните следующую команду:

    ```bash
    zypper refresh
    ```

2. Чтобы обновить систему, выполните следующую команду:

    ```bash
    zypper update
    ```

Действия команды [zypper](https://en.opensuse.org/Portal:Zypper), такие как установка и удаление пакетов, записываются в файл журнала `/var/log/zypper.log`. 

## <a name="about-the-azcmagent-tool"></a>Сведения о средстве Azcmagent

Средство Azcmagent (Azcmagent.exe) используется для настройки агента Connected Machine в Azure Arc для серверов (предварительная версия) во время установки или для изменения начальной конфигурации агента после установки. Azcmagent.exe предоставляет параметры командной строки для настройки агента и просмотра его состояния:

* **Connect** (Подключить). Чтобы подключить компьютер к Azure Arc.

* **Disconnect** (Отключить). Чтобы отключить компьютер от Azure Arc.

* **Reconnect** (Повторное подключение). Чтобы повторно подключить отключенный компьютер к службе Azure Arc.

* **Show** (Показать). Просмотр состояния агента и его свойств конфигурации (имя группы ресурсов, идентификатора подписки, версии и т. д.), которые могут помочь при устранении неполадок с агентом.

* **-h или --help**. Отображает возможные параметры командной строки.

    Например, чтобы просмотреть подробную справку по параметру **Reconnect** (Повторное подключение), введите `azcmagent reconnect -h`. 

* **-v или --verbose**. Включение подробного ведения журнала.

Вы можете выполнить команды **Connect** (Подключить), **Disconnect** (Отключить) и **Reconnect** (Повторное подключение) вручную, войдя в систему интерактивно, или автоматизированно с помощью того же субъекта-службы, который использовался для подключения нескольких агентов, или с [маркером доступа](../../active-directory/develop/access-tokens.md) платформы удостоверений Майкрософт. Если вы не использовали субъект-службу для регистрации компьютера в Azure Arc для серверов (предварительная версия), ознакомьтесь с [этой статьей](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale), чтобы создать субъект-службу.

### <a name="connect"></a>Подключение

Этот параметр указывает ресурс в Azure Resource Manager, представляющий компьютер, который создается в Azure. Ресурс находится в указанной подписке и группе ресурсов, а данные о компьютере хранятся в регионе Azure, указанном параметром `--location`. Имя ресурса по умолчанию является именем узла этого компьютера, если оно не указано.

Затем сертификат, соответствующий назначенному системой идентификатору компьютера, скачивается и сохраняется локально. После завершения этого шага Служба метаданных Azure Connected Machine и агент гостевой конфигурации начинают синхронизацию с Azure Arc для серверов (предварительная версия).

Чтобы подключиться с помощью субъекта-службы, выполните следующую команду:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Чтобы подключиться с помощью маркера доступа, выполните следующую команду:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Чтобы подключиться с помощью учетных данных (интерактивно) с повышенными правами, выполните следующую команду:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Отключить

Этот параметр указывает ресурс в Azure Resource Manager, представляющий компьютер, который удаляется в Azure. Он не удаляет агент с компьютера, это необходимо сделать отдельно. Если после отключения компьютера вы хотите повторно зарегистрировать его в Azure Arc для серверов (предварительная версия), используйте `azcmagent connect`, чтобы для него был создан ресурс в Azure.

Чтобы отключиться с помощью субъекта-службы, выполните следующую команду:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Чтобы отключиться с помощью маркера доступа, выполните следующую команду:

`azcmagent disconnect --access-token <accessToken>`

Чтобы отключиться с помощью учетных данных (интерактивно) с повышенными правами, выполните следующую команду:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Повтор соединения

Этот параметр повторно подключает уже зарегистрированный или подключенный компьютер с помощью Azure Arc для серверов (предварительная версия). Это может потребоваться, если компьютер был отключен не менее 45 дней и срок действия его сертификата истек. Для получения новых учетных данных этот параметр использует варианты проверки подлинности, соответствующие ресурсу Azure Resource Manager, представляющему этот компьютер.

Эта команда требует более высоких привилегий, чем роль [Подключение Azure Connected Machine](agent-overview.md#required-permissions).

Чтобы повторно подключиться с помощью субъекта-службы, выполните следующую команду:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Чтобы повторно подключиться с помощью маркера доступа, выполните следующую команду:

`azcmagent reconnect --access-token <accessToken>`

Чтобы повторно подключиться с помощью учетных данных (интерактивно) с повышенными правами, выполните следующую команду:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Удаление агента

Воспользуйтесь одним из следующих методов, чтобы удалить агент Connected Machine для Windows или Linux с компьютера. Удаление агента не приведет к отмене регистрации компьютера в Arc для серверов (предварительная версия), так как это отдельный процесс, который выполняется, если больше не требуется управлять компьютером в Azure.

### <a name="windows-agent"></a>Агент Windows

Оба следующих метода удаляют агент, но не удаляют папку *C:\Program Files\AzureConnectedMachineAgent* на компьютере.

#### <a name="uninstall-from-control-panel"></a>Удаление из панели управления

1. Чтобы удалить агент Windows с компьютера, выполните следующие действия:

    а. Войдите в систему компьютера, используя учетную запись с правами администратора.  
    b. Откройте **Панель управления**, выберите раздел **Программы и компоненты**.  
    c. В разделе **Программы и компоненты** выберите **Azure Connected Machine Agent** (Агент подключенного компьютера Azure), затем **Удалить**, а затем **Да**.  

    >[!NOTE]
    > Можно также запустить мастер установки агента, дважды щелкнув пакет установщика **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Удаление из командной строки

Чтобы вручную удалить агент из командной строки или использовать автоматизированный метод, например скрипт, можно использовать следующий пример. Сначала из операционной системы необходимо извлечь код продукта, который является уникальным идентификатором, представляющим собой идентификатор субъекта пакета приложения. Удаление выполняется с помощью командной строки Msiexec.exe — `msiexec /x {Product Code}`.

1. откройте редактор реестра;

2. в разделе реестра `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` найдите и скопируйте код GUID продукта;

3. после этого агент можно удалить с помощью Msiexec, используя следующие примеры:

   * из командной строки введите:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * эти же действия можно выполнить с помощью PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Агент Linux

> [!NOTE]
> Чтобы удалить агент, необходимо иметь разрешения *корневого* доступа или учетную запись, которой повысили права с помощью команды Sudo.

Чтобы удалить агент Linux, используемая команда определяется операционной системой Linux.

- В ОС Ubuntu выполните следующую команду:

    ```bash
    sudo apt purge azcmagent
    ```

- В RHEL, CentOS и Amazon Linux выполните следующую команду:

    ```bash
    sudo yum remove azcmagent
    ```

- В ОС SLES выполните следующую команду:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Отмена регистрации компьютера

Если вы планируете прекратить управление компьютером с помощью вспомогательных служб в Azure, выполните следующие действия, чтобы отменить регистрацию компьютера в Arc для серверов (предварительная версия). Эти действия можно выполнить либо до, либо после удаления агента Connected Machine с компьютера.

1. Перейдите на [портал Azure](https://aka.ms/hybridmachineportal) и откройте Azure Arc для серверов (предварительная версия).

2. Выберите компьютер в списке, щелкните значок с многоточием ( **...** ), а затем выберите пункт **Удалить**.

## <a name="update-or-remove-proxy-settings"></a>Обновление или удаление параметров прокси-сервера

Чтобы после развертывания настроить взаимодействие агента со службой через прокси-сервер или удалить эту конфигурацию, используйте один из приведенных ниже способов.

### <a name="windows"></a>Windows

Чтобы задать переменную среды прокси-сервера, выполните следующую команду:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Чтобы настроить агент для прекращения связи через прокси-сервер, выполните следующую команду для удаления переменной среды прокси-сервера и перезапуска службы агента:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Чтобы задать прокси-сервер, выполните следующую команду из каталога, в который вы скачали пакет установки агента:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Чтобы настроить агент для прекращения связи через прокси-сервер, выполните следующую команду для удаления конфигурации прокси-сервера:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять компьютером с помощью [Политики Azure](../../governance/policy/overview.md), например для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) и т. д.

- Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью возможностей, таких как Управление обновлениями, или использовать другие службы Azure, например [Центр безопасности Azure](../../security-center/security-center-intro.md).
