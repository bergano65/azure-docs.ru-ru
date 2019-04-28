---
title: Управление агентом Azure Log Analytics | Документация Майкрософт
description: В этой статье описаны разные задачи управления, которые обычно можно выполнять в течение жизненного цикла развернутого на компьютере агента Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: magoedte
ms.openlocfilehash: 19530aa676e681f9a6ec50d2cacf77711dcb0110
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764090"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Обслуживание агента Log Analytics для Windows и Linux и управление им

После первоначального развертывания агента Linux в Azure Monitor или Log Analytics Windows может потребоваться перенастроить агент, обновить его или удалить его из компьютера, если был достигнут в рабочей области вывода из эксплуатации, его жизненного цикла. Вы можете легко управлять этими задачами обслуживания вручную или автоматизировать этот процесс, сократив при этом эксплуатационные расходы и количество ошибок.

## <a name="upgrading-agent"></a>Обновление агента

Агент Log Analytics для Windows и Linux можно обновить до последней версии вручную или автоматически в зависимости от сценария развертывания и среды, в которой запущена виртуальная машина. Следующие методы могут использоваться для обновления агента.

| Среда | Метод установки | Метод обновления |
|--------|----------|-------------|
| Azure | Расширение LOG Analytics агента виртуальной Машины для Windows и Linux | Агент автоматически обновляется по умолчанию, если вы не настроили шаблон Azure Resource Manager для отказаться, задав свойство *autoUpgradeMinorVersion* для **false**. |
| Пользовательские образы виртуальных Машин Azure | Установка агента Log Analytics для Windows и Linux вручную | Обновление до последней версии агента виртуальных машин должен выполняться из командной строки, для запуска пакета установщика Windows или Linux самораспаковывающемся и устанавливаемом пакете скриптов оболочки.|
| Виртуальные машины не относящиеся к Azure | Установка агента Log Analytics для Windows и Linux вручную | Обновление до последней версии агента виртуальных машин должен выполняться из командной строки, для запуска пакета установщика Windows или Linux самораспаковывающемся и устанавливаемом пакете скриптов оболочки. |

### <a name="upgrade-windows-agent"></a>Агент обновления Windows 

Чтобы обновить агент на виртуальной Машине Windows до последней версии не устанавливаются при использовании расширения виртуальной Машины Log Analytics, можно выполнять из командной строки, сценария или другого решения автоматизации, или с помощью MMASetup -\<платформы\>MSI-файл программы установки Мастер.  

Можно загрузить последнюю версию агента Windows в рабочей области Log Analytics, выполнив следующие действия.

1. Войдите на портал Azure.

2. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Рабочие области Log Analytics**.

3. В списке рабочих областей Log Analytics выберите рабочую область.

4. В рабочей области Log Analytics, выберите **Дополнительные параметры**, а затем выберите **подключенные источники**и, наконец **серверов Windows**.

5. Из **серверов Windows** странице, выберите соответствующий **загрузить агент Windows** версию для загрузки в зависимости от архитектуры процессора операционной системы Windows.

>[!NOTE]
>Во время обновления агента Log Analytics для Windows он не поддерживает настройку или повторной настройки для отправки отчетов в рабочей области. Чтобы настроить агент, необходимо воспользоваться одним из способов, перечисленных в разделе [Добавление или удаление рабочей области](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Обновление с помощью мастера установки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Выполнение **MMASetup -\<платформы\>.exe** для запуска мастера установки.

3. На первой странице мастера установки, нажмите кнопку **Далее**.

4. В **Установка Microsoft Monitoring Agent** диалоговом окне щелкните **принимаю** принять условия лицензионного соглашения.

5. В **Установка Microsoft Monitoring Agent** диалоговом окне щелкните **обновление**. На странице состояния отображается ход выполнения обновления.

6. Когда **Настройка Microsoft Monitoring Agent успешно завершена.** Откроется страница, нажмите кнопку **Готово**.

#### <a name="to-upgrade-from-the-command-line"></a>Для обновления из командной строки

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Чтобы извлечь файлы установки агента из командной строки с повышенными привилегиями, запустите файл `MMASetup-<platform>.exe /c`. После этого вы сможете выбрать путь для извлечения файлов. Кроме того, путь можно указать, передав аргументы `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Выполните следующую команду, где D:\ — расположение для файла журнала обновления.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Обновление агента Linux 

Обновление с предыдущих версий (> ниже 1.0.0-47) поддерживается. При установке с помощью команды `--upgrade` все компоненты агента будут обновлены до последней версии.

Выполните следующую команду, чтобы обновить агент.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Добавление или удаление рабочей области

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-from-control-panel"></a>Обновление параметров на панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Откройте **Панель управления**.

3. Выберите **Microsoft Monitoring Agent** и перейдите на вкладку **Azure Log Analytics**.

4. Чтобы удалить рабочую область, выберите нужную и нажмите кнопку **Удалить**. Повторите этот шаг для всех рабочих областей, которые этот агент больше не должен отслеживать.

5. Чтобы добавить рабочую область, нажмите кнопку **Добавить**, а затем в диалоговом окне **Add a Log Analytics Workspace** (Добавление рабочей области Log Analytics) введите идентификатор и ключ (первичный) рабочей области. Если компьютер должен передавать данные в рабочую область Log Analytics в облаке Azure для государственных организаций, выберите "Azure для государственных организаций США" из раскрывающегося списка "Облако Azure".

6. Нажмите кнопку **ОК** , чтобы сохранить изменения.

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

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Добавление рабочей области в Azure для государственных организаций США с помощью PowerShell

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
Ниже описывается, как перенастроить агент для Linux, если вы решили зарегистрировать его в другой рабочей области или хотите удалить рабочую область из его конфигурации.

1. Чтобы убедиться, что он зарегистрирован в рабочей области, выполните следующую команду.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Результат выполнения команды должен иметь следующий вид.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Важно, чтобы по состоянию было видно, что агент выполняется. В противном случае приведенные ниже шаги по перенастройке агент выполнить не удастся.

2. Если он уже зарегистрирован в рабочей области, удалите ее, выполнив следующую команду. Если агент еще не зарегистрирован, перейдите к следующему шагу.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Чтобы зарегистрировать агент в другой рабочей области, выполните следующую команду:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Чтобы проверить внесенные изменения, выполните команду:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Результат выполнения команды должен иметь следующий вид.

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Службу агента не нужно перезапускать, чтобы изменения вступили в силу.

## <a name="update-proxy-settings"></a>Обновление параметров прокси-сервера
Чтобы после развертывания настроить взаимодействие агента со службой через прокси-сервер или [шлюз Log Analytics](gateway.md), используйте один из приведенных ниже способов.

### <a name="windows-agent"></a>Агент Windows

#### <a name="update-settings-using-control-panel"></a>Обновление параметров с помощью панели управления

1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. Откройте **Панель управления**.

3. Выберите **Microsoft Monitoring Agent** и перейдите на вкладку **Параметры прокси-сервера**.

4. Щелкните **Использовать прокси-сервер** и укажите URL-адрес и номер порта прокси-сервера или шлюза. Если для доступа к прокси-серверу или шлюзу Log Analytics требуется аутентификация, введите имя пользователя и пароль, а затем нажмите кнопку **ОК**.

#### <a name="update-settings-using-powershell"></a>Обновление параметров с помощью PowerShell

Скопируйте следующий образец кода PowerShell, измените в нем сведения, относящиеся к используемой среде, и сохраните его в файле с расширением PS1. Запустите сценарий на каждом компьютере, который подключается непосредственно к рабочей области Log Analytics в Azure Monitor.

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
Чтобы удалить агент Windows или Linux с помощью мастера установки или командной строки, используйте одну из приведенных ниже процедур.

### <a name="windows-agent"></a>Агент Windows

#### <a name="uninstall-from-control-panel"></a>Удаление из панели управления
1. Войдите в систему компьютера, используя учетную запись с правами администратора.

2. На **панели управления** выберите **Программы и компоненты**.

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

2. Откройте **Панель управления**.

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

Если во время установки или настройки агента возникают какие-либо проблемы, см. статью об [устранении неполадок с агентом Linux для Log Analytics](agent-linux-troubleshoot.md).
