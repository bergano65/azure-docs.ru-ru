---
title: Управление агентом аналитики журналов Azure
description: В этой статье описаны различные задачи управления, которые вы обычно выполняете в течение жизненного цикла агента Log Analytics Windows или Linux, развернутого на компьютере.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275104"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Обслуживание агента Log Analytics для Windows и Linux и управление им

После первоначального развертывания агента Log Analytics Windows или Linux в Azure Monitor может потребоваться перенастроить агента, обновить его или удалить с компьютера, если он достиг стадии выхода на пенсию в своем жизненном цикле. Вы можете легко управлять этими задачами обслуживания вручную или автоматизировать этот процесс, сократив при этом эксплуатационные расходы и количество ошибок.

## <a name="upgrading-agent"></a>Обновление агента

Агент Log Analytics для Windows и Linux может быть обновлен до последнего выпуска вручную или автоматически в зависимости от сценария развертывания и среды, в которой работает VM. Для обновления агента можно использовать следующие методы.

| Среда | Метод установки | Метод перехода |
|--------|----------|-------------|
| Azure | Выдвижение агента VM для Windows/Linux агента «Аналитика журнала» | Агент автоматически обновляется по умолчанию, если вы не настроили шаблон менеджера ресурсов Azure, чтобы отказаться, установив свойство *autoUpgradeMinorMinorVersion* на **ложный.** |
| Пользовательские изображения Azure VM | Ручная установка агента журналной аналитики для Windows/Linux | Обновление ВМ до новейшей версии агента должно быть выполнено из командной строки, работающей с пакетом установки Windows или пакетом скриптов Linux, извлекающим самоизвечивание и устанавливаемый пакет сценариев оболочки.|
| Не-Azure VMs | Ручная установка агента журналной аналитики для Windows/Linux | Обновление ВМ до новейшей версии агента должно быть выполнено из командной строки, работающей с пакетом установки Windows или пакетом скриптов Linux, извлекающим самоизвечивание и устанавливаемый пакет сценариев оболочки. |

### <a name="upgrade-windows-agent"></a>Обновление агента Windows 

Чтобы обновить агент на Windows VM до последней версии, не установленной с помощью расширения Log Analytics VM, вы либо\<\>работаете с командным запросом, скриптом или другим решением автоматизации, либо с помощью платформы MMASetup .  

Вы можете скачать последнюю версию агента Windows из рабочего пространства Log Analytics, выполнив следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).

2. На портале Azure щелкните **все сервисы**. В списке ресурсов введите **log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Рабочие области Log Analytics**.

3. В списке рабочих областей анализа журналов выберите рабочее пространство.

4. В рабочем пространстве log Analytics выберите **Расширенные настройки,** затем выберите **подключенные источники**и, наконец, **серверы Windows.**

5. На странице **Windows Servers** выберите соответствующую версию Download **Windows Agent** для загрузки в зависимости от архитектуры процессора операционной системы Windows.

>[!NOTE]
>Во время обновления агента Log Analytics для Windows он не поддерживает настройку или перенастройку рабочего пространства для отчета. Чтобы настроить агента, необходимо следовать одному из поддерживаемых методов, перечисленных в [добавлении или удалении рабочего пространства.](#adding-or-removing-a-workspace)
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Обновление с помощью мастера настройки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Выполнить **MMASetup-платформа\<\>.exe,** чтобы начать установку мастера.

3. На первой странице мастера настройки нажмите **кнопку «Следующий**».

4. В диалоговом окне установки **агента microsoft Monitoring,** нажмите **кнопку Я согласен** принять лицензионное соглашение.

5. В диалоговом окне **Установка Microsoft Monitoring Agent** нажмите кнопку **Обновить**. На странице состояния отображается ход обновления.

6. При **успешной завершении конфигурации агента мониторинга Майкрософт.** страница появляется, нажмите **Закончить**.

#### <a name="to-upgrade-from-the-command-line"></a>Обновление от командной строки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Чтобы извлечь файлы установки агента из командной строки с повышенными привилегиями, запустите файл `MMASetup-<platform>.exe /c`. После этого вы сможете выбрать путь для извлечения файлов. Кроме того, путь можно указать, передав аргументы `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Выполните следующую команду (D:\ является местонахождением файла журнала обновления).

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Обновление агента Linux 

Обновление от предыдущих версий (>1.0.0-47) поддерживается. При установке с помощью команды `--upgrade` все компоненты агента будут обновлены до последней версии.

Выполнить следующую команду для обновления агента.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Добавление или удаление рабочей области

### <a name="windows-agent"></a>Агент Windows
Шаги в этом разделе необходимы, когда требуется не только перенастроить агента Windows, чтобы отчитаться в другом рабочем пространстве или удалить рабочее пространство из его конфигурации, но и когда вы хотите настроить агента, чтобы сообщить более чем одному рабочему пространству (обычно называют мульти-homing). Настройка агента Windows для отчета в нескольких рабочих областях может быть выполнена только после первоначальной настройки агента и с использованием методов, описанных ниже.    

#### <a name="update-settings-from-control-panel"></a>Обновление параметров на панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Откройте **панель управления**.

3. Выберите **Microsoft Monitoring Agent** и перейдите на вкладку **Azure Log Analytics**.

4. Чтобы удалить рабочую область, выберите нужную и нажмите кнопку **Удалить**. Повторите этот шаг для всех рабочих областей, которые этот агент больше не должен отслеживать.

5. Чтобы добавить рабочую область, нажмите кнопку **Добавить**, а затем в диалоговом окне **Add a Log Analytics Workspace** (Добавление рабочей области Log Analytics) введите идентификатор и ключ (первичный) рабочей области. Если компьютер должен передавать данные в рабочую область Log Analytics в облаке Azure для государственных организаций, выберите "Azure для US Gov организаций" из раскрывающегося списка "Облако Azure".

6. Нажмите кнопку **ОК**, чтобы сохранить изменения.

#### <a name="remove-a-workspace-using-powershell"></a>Удаление рабочей области с помощью PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Добавление рабочей области в Azure для коммерческих организаций с помощью PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Добавление рабочей области в Azure для US Gov организаций с помощью PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Если ранее вы устанавливали или настраивали агент с помощью командной строки или сценария, теперь вместо `EnableAzureOperationalInsights` используется `AddCloudWorkspace` и `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Агент Linux
Следующие шаги демонстрируют, как перенастроить агента Linux, если вы решите зарегистрировать его в другом рабочем пространстве или удалить рабочее пространство из его конфигурации.

1. Чтобы убедиться, что он зарегистрирован в рабочей области, выполните следующую команду.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Результат выполнения команды должен иметь следующий вид.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Важно, чтобы по состоянию было видно, что агент выполняется. В противном случае приведенные ниже шаги по перенастройке агент выполнить не удастся.

2. Если он уже зарегистрирован в рабочей области, удалите ее, выполнив следующую команду. Если агент еще не зарегистрирован, перейдите к следующему шагу.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Чтобы зарегистрировать агент в другой рабочей области, выполните следующую команду:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Чтобы проверить, что изменения вступили в силу, запустите следующую команду:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Результат выполнения команды должен иметь следующий вид.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Службу агента не нужно перезапускать, чтобы изменения вступили в силу.

## <a name="update-proxy-settings"></a>Обновление параметров прокси-сервера
Чтобы после развертывания настроить взаимодействие агента со службой через прокси-сервер или [шлюз Log Analytics](gateway.md), используйте один из приведенных ниже способов.

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-using-control-panel"></a>Обновление параметров с помощью панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Откройте **панель управления**.

3. Выберите **агента мониторинга Майкрософт,** а затем нажмите на вкладку **«Настройки прокси».**

4. Щелкните **Использовать прокси-сервер** и укажите URL-адрес и номер порта прокси-сервера или шлюза. Если для доступа к прокси-серверу или шлюзу Log Analytics требуется аутентификация, введите имя пользователя и пароль, а затем нажмите кнопку **ОК**.

#### <a name="update-settings-using-powershell"></a>Обновление параметров с помощью PowerShell

Скопируйте следующий образец кода PowerShell, измените в нем сведения, относящиеся к используемой среде, и сохраните его в файле с расширением PS1. Запустите скрипт на каждом компьютере, который подключается непосредственно к рабочему пространству log Analytics в Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Агент Linux
Если компьютерам Linux нужно взаимодействовать через прокси-сервер или шлюз Log Analytics, выполните приведенные ниже действия. Значение конфигурации прокси-сервера имеет следующий синтаксис: `[protocol://][user:password@]proxyhost[:port]`. Свойство *proxyhost* принимает полное доменное имя или IP-адрес прокси-сервера.

1. Измените файл `/etc/opt/microsoft/omsagent/proxy.conf`, выполнив следующие команды и указав собственные значения параметров.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Перезапустите агент, выполнив следующую команду.

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Удаление агента
Используйте одну из следующих процедур для удаления агента Windows или Linux с помощью командной строки или мастера настройки.

### <a name="windows-agent"></a>Агент Windows

#### <a name="uninstall-from-control-panel"></a>Удаление из панели управления
1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. На **панели управления** щелкните элемент **Программы и компоненты**.

3. В окне **Программы и компоненты** выберите **Microsoft Monitoring Agent**, щелкните **Удалить**, а затем нажмите кнопку **Да**.

>[!NOTE]
>Мастер настройки агента можно также запустить, дважды щелкнув файл **MMASetup-\<platform\>.exe**, который можно скачать из рабочей области на портале Azure.

#### <a name="uninstall-from-the-command-line"></a>Удаление из командной строки
Скачанный файл агента — это автономный пакет установки, созданный IExpress. В нем содержатся программа установки агента и вспомогательные файлы. Чтобы удалить агент с помощью командной строки, содержимое этого пакета нужно извлечь. Ниже описано, как это сделать.

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Чтобы извлечь файлы установки агента из командной строки с повышенными привилегиями, запустите файл `extract MMASetup-<platform>.exe`. После этого вы сможете выбрать путь для извлечения файлов. Кроме того, путь можно указать, передав аргументы `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Дополнительные сведения о параметрах командной строки, поддерживаемых IExpress, см. в [этой статье](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages). Затем обновите пример в соответствии со своими потребностями.

3. В командной строке введите следующую команду: `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Агент Linux
Чтобы удалить агент, выполните следующую команду на компьютере Linux. Аргумент *--purge* полностью удаляет агент и его конфигурацию.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Настройка передачи агентом отчетов в группу управления Operations Manager

### <a name="windows-agent"></a>Агент Windows
Чтобы в агенте Log Analytics для Windows настроить передачу отчетов в группу управления System Center Operations Manager, выполните приведенные ниже действия.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Откройте **панель управления**.

3. Щелкните **Microsoft Monitoring Agent** и перейдите на вкладку **Operations Manager**.

4. Если серверы Operations Manager интегрированы с Active Directory, установите флажок **Автоматически обновлять назначения групп управления из AD DS**.

5. Нажмите кнопку **Добавить**, чтобы открыть диалоговое окно **Добавление группы управления**.

6. В поле **Имя группы управления** введите имя группы управления.

7. В поле **Основной сервер управления** введите имя компьютера основного сервера управления.

8. В поле **Порт сервера управления** введите номер порта TCP.

9. На странице **Учетная запись действия агента**выберите учетную запись Local System или учетную запись локального домена.

10. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Добавление группы управления**, и еще раз нажмите **ОК**, чтобы закрыть диалоговое окно **Свойства Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Агент Linux
Чтобы в агенте Log Analytics для Linux настроить передачу отчетов в группу управления System Center Operations Manager, выполните приведенные ниже действия.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Измените файл `/etc/opt/omi/conf/omiserver.conf`

2. Укажите для строки `httpsport=` номер порта 1270. Например: `httpsport=1270`.

3. Перезапустите сервер OMI: `sudo /opt/omi/bin/service_control restart`.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [проблему с агентом Linux,](agent-linux-troubleshoot.md) если вы столкнулись с проблемами при установке или управлении агентом Linux.

- Просмотрите [проблему с агентом Windows,](agent-windows-troubleshoot.md) если вы столкнулись с проблемами при установке или управлении агентом Windows.
