---
title: Включить Azure Monitor для гибридной среды Документы Майкрософт
description: В этой статье описывается, как вы включаете Azure Monitor для виртуальных технологий для гибридной облачной среды, содержащей одну или несколько виртуальных машин.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480748"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Включите Azure Monitor для vMs для гибридной среды

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных компьютеров или физических компьютеров, размещенных в центре обработки данных или другой облачной среде. В конце этого процесса вы успешно начнете мониторинг ваших виртуальных машин в вашей среде и узнаете, испытывают ли они какие-либо проблемы с производительностью или доступностью.

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](vminsights-enable-overview.md) и убедитесь, что подписка и ресурсы соответствуют требованиям. Ознакомьтесь с требованиями и методами развертывания для [агента Log Analytics в Linux и Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Dependency Agent в Azure Monitor для виртуальных машин не передает данные и не требует изменений в настройках брандмауэра или портов. Данные Карты всегда передаются агентом Log Analytics в службу Azure Monitor напрямую или через [шлюз Управления Операциями,](../../azure-monitor/platform/gateway.md) если политика ИТ-безопасности не позволяет компьютерам в сети подключаться к Интернету.

Шаги для выполнения этой задачи суммируются следующим образом:

1. Установка агента по анализу журналов для Windows или Linux. Перед установкой агента просмотрите статью [обзора агента log Analytics,](../platform/log-analytics-agent.md) чтобы понять предпосылки системы и методы развертывания.

2. Скачайте и установите Dependency Agent схемы Azure Monitor для виртуальных машин [Windows](https://aka.ms/dependencyagentwindows) или [Linux](https://aka.ms/dependencyagentlinux).

3. Включите сбор данных счетчиков производительности.

4. Разверните Azure Monitor для виртуальных машин.

>[!NOTE]
>Информация, описанная в этой статье для развертывания агента зависимости, также применима к [решению Service Map.](service-map.md)  

## <a name="install-the-dependency-agent-on-windows"></a>Установка Dependency Agent на Windows

Dependency Agent можно вручную установить на компьютерах Windows с помощью `InstallDependencyAgent-Windows.exe`. Если запустить этот исполняемый файл без параметров, запустится мастер для установки агента в интерактивном режиме.

>[!NOTE]
>Для установки или удаления агента требуются права *администратора*.

В приведенной ниже таблице выделены параметры, поддерживаемые при установке агента из командной строки.

| Параметр | Описание |
|:--|:--|
| /? | Возвращает список параметров командной строки. |
| /S | Автоматическая установка без взаимодействия с пользователем. |

Например, чтобы запустить программу `/?` установки с параметром, введите **InstallDependencyAgent-Windows.exe /?**.

Файлы для Windows Dependency Agent по умолчанию устанавливаются в папку *C:\Program Files\Microsoft Dependency Agent*. Если агент зависимости не может начаться после завершения установки, проверьте журналы для получения подробной информации об ошибке. Каталогом журналов является *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Установка Dependency Agent в Linux

Dependency Agent устанавливается на серверах Linux с помощью файла *InstallDependencyAgent-Linux64.bin*. Это сценарий оболочки с самоизвлекающимся двоичным файлом. Вы можете запустить этот файл с помощью `sh` или добавить разрешения на выполнение в сам файл.

>[!NOTE]
> Для установки или настройки агента требуется доступ с правами привилегированного пользователя.
>

| Параметр | Описание |
|:--|:--|
| -help | Получает список параметров командной строки. |
| -S | Выполняет автоматическую установку без вывода сообщений для пользователя. |
| --check | Проверка разрешений и операционной системы без установки агента. |

Например, чтобы запустить программу `-help` установки с параметром, введите **InstallDependencyAgent-Linux64.bin-help**.

Установите агент зависимости Linux в `sh InstallDependencyAgent-Linux64.bin`качестве корня, запустив команду.

Если Dependency Agent не запускается, просмотрите подробные сведения об ошибке в записях журналов. Что на агентах Linux, каталог журнала */var/opt/microsoft/dependency-agent/log*.

Файлы для Dependency Agent размещаются в следующих каталогах:

| Файлы | Расположение |
|:--|:--|
| Основные файлы | /opt/microsoft/dependency-agent |
| Файлы журналов | /var/opt/microsoft/dependency-agent/log |
| Файлы конфигурации | /etc/opt/microsoft/dependency-agent/config |
| Исполняемые файлы службы | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Двоичные файлы хранилища | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Примеры скриптов установки

Чтобы легко развернуть Dependency Agent на нескольких серверах за один раз, воспользуйтесь следующим примером скрипта для загрузки и установки Dependency Agent в ОС Windows или Linux.

### <a name="powershell-script-for-windows"></a>Скрипт PowerShell для Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Скрипт оболочки для Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Настройка требуемого состояния (DSC)

Чтобы развернуть Dependency Agent с помощью Desired State Configuration, можно использовать модуль xPSDesiredStateConfiguration со следующим примером кода.

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Включение счетчиков производительности

Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Вы можете сделать это одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* Загрузив и запустив скрипт PowerShell, который доступен в [галерее Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Развертывание Azure Monitor для виртуальных машин

В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знаете, как развертывать ресурсы с помощью шаблона, см.:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Для установки или обновления Azure CLI [см.](https://docs.microsoft.com/cli/azure/install-azure-cli)

### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Сохраните этот файл в локальную папку, присвоив ему имя *installsolutionsforvminsights.json*.

1. Соберите значения параметров *WorkspaceName*, *ResourceGroupName* и *WorkspaceLocation*. Значение для *WorkspaceName* — это имя рабочей области Log Analytics. Значение *WorkspaceLocation* — регион, в котором определена рабочая область.

1. Теперь все готово для развертывания этого шаблона с помощью следующей команды PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Изменение конфигурации может занять несколько минут. Когда оно закончено, сообщение отображается, что похоже на следующее и включает в себя результат:

    ```powershell
    provisioningState       : Succeeded
    ```
   После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть сведения о состоянии работоспособности и метрики для гибридного компьютера.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="vm-doesnt-appear-on-the-map"></a>VM не отображается на карте

Если установка агента зависимости удалась, но вы не видите компьютер на карте, диагностируйте проблему, выпустив эти действия.

1. Успешно ли установлен Dependency Agent? Для этого проверьте, установлена и запущена ли служба.

    **Windows**: Ищите службу под названием "Агент зависимости Microsoft".

    **Linux**: Ищите запущенный процесс "Microsoft-зависимость-агент".

2. Вы на [бесплатный уровень ценообразования журнала Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Бесплатный план позволяет до пяти уникальных компьютеров. Любые последующие компьютеры не будут отображаться на карте, даже если предыдущие пять больше не отправляют данные.

3. Отправляет ли компьютер данные журнала и перф в журналы Мониторингов Azure? Выполните следующий запрос для вашего компьютера:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Вернул ли он один или несколько результатов? Это последние данные? Если это так, ваш агент Log Analytics работает правильно и общается с службой. Если это не так, проверьте агент на сервере. Дополнительные сведения см. в разделах [Устранение неполадок ориентирования Management Suite операций](../platform/agent-windows-troubleshoot.md) или [Устранение неполадок с агентом Azure Log Analytics для Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Компьютер отображается на карте, но не имеет процессов

Если вы видите ваш сервер на карте, но у него нет данных процесса или соединения, это указывает на то, что агент зависимости установлен и работает, но драйвер ядра не загружается.

Проверьте файл C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) или /var/opt/microsoft/dependency-agent/log/service.log (Linux). В последних строках файла должно быть указано, почему не удалось загрузить ядро. Например, если вы обновили ядро, оно может не поддерживаться в Linux.


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда мониторинг включен для ваших виртуальных машин, эта информация доступна для анализа с Azure Monitor для виртуальных технологий.

- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).

- Чтобы определить узкие места и общее использование с производительностью VM, [см.](vminsights-performance.md)
