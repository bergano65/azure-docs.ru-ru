---
title: Включение Azure Monitor (Предварительная версия) для гибридной среды | Документация Майкрософт
description: В этой статье описывается, как включить Azure Monitor для виртуальных машин для гибридной облачной среды, содержащей одну или несколько виртуальных машин.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: e9e5504125920cedaf383f8fa4299a4b1b1d60ed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553866"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Включение Azure Monitor для виртуальных машин (Предварительная версия) для гибридной среды

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных машин (Предварительная версия) для виртуальных машин или физических компьютеров, размещенных в вашем центре обработки данных или в другой облачной среде. По завершении этого процесса вы сможете успешно приступили к наблюдению за виртуальными машинами в среде и узнать, испытывает ли они проблемы с производительностью или доступностью. 

Прежде чем начать, обязательно ознакомьтесь с [предварительными](vminsights-enable-overview.md) требованиями и убедитесь, что подписка и ресурсы соответствуют требованиям. Ознакомьтесь с требованиями и методами развертывания для [агента Log Analytics в Linux и Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Dependency Agent в Azure Monitor для виртуальных машин не передает данные и не требует изменений в настройках брандмауэра или портов. Данные карт всегда передаются агентом Log Analytics в службу Azure Monitor, либо напрямую, либо через [шлюз Operations Management Suite](../../azure-monitor/platform/gateway.md) , если политики безопасности ИТ не разрешают компьютерам сети подключаться к Интернету.

Ниже приведены шаги для выполнения этой задачи.

1. Установите агент Log Analytics для Windows или Linux. Перед установкой агента ознакомьтесь со статьей [Обзор агента log Analytics](../platform/log-analytics-agent.md) , чтобы узнать о необходимых компонентах и методах развертывания.

2. Скачайте и установите Dependency Agent схемы Azure Monitor для виртуальных машин [Windows](https://aka.ms/dependencyagentwindows) или [Linux](https://aka.ms/dependencyagentlinux).

3. Включите сбор данных счетчиков производительности.

4. Разверните Azure Monitor для виртуальных машин.

>[!NOTE]
>Сведения, описанные в этой статье для развертывания агента зависимостей, также применимы к [решению сопоставление служб](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Установка Dependency Agent на Windows

Dependency Agent можно вручную установить на компьютерах Windows с помощью `InstallDependencyAgent-Windows.exe`. Если запустить этот исполняемый файл без параметров, запустится мастер для установки агента в интерактивном режиме.

>[!NOTE]
>Для установки или удаления агента требуются права *администратора*.

В приведенной ниже таблице выделены параметры, поддерживаемые при установке агента из командной строки.

| Параметр | Описание |
|:--|:--|
| /? | Возвращает список параметров командной строки. |
| /S | Автоматическая установка без взаимодействия с пользователем. |

Например, чтобы запустить программу установки с параметром `/?`, введите **файла installdependencyagent-Windows. exe/?** .

Файлы для Windows Dependency Agent по умолчанию устанавливаются в папку *C:\Program Files\Microsoft Dependency Agent*. Если не удается запустить агент зависимостей после завершения установки, просмотрите подробные сведения об ошибке в журналах. Каталогом журналов является *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Установка Dependency Agent в Linux

Dependency Agent устанавливается на серверах Linux с помощью файла *InstallDependencyAgent-Linux64.bin*. Это сценарий оболочки с самоизвлекающимся двоичным файлом. Вы можете запустить этот файл с помощью `sh` или добавить разрешения на выполнение в сам файл.

>[!NOTE]
> Для установки или настройки агента требуется доступ с правами привилегированного пользователя.
>

| Параметр | Описание |
|:--|:--|
| -help | Получает список параметров командной строки. |
| -s | Выполняет автоматическую установку без вывода сообщений для пользователя. |
| --check | Проверка разрешений и операционной системы без установки агента. |

Например, чтобы запустить программу установки с параметром `-help`, введите **InstallDependencyAgent-Linux64. bin-Help**.

Установите агент зависимостей Linux в качестве привилегированного пользователя, выполнив команду `sh InstallDependencyAgent-Linux64.bin`.

Если Dependency Agent не запускается, просмотрите подробные сведения об ошибке в записях журналов. В агентах Linux каталог журнала находится в расположении */var/opt/microsoft/dependency-agent/log*.

Файлы для Dependency Agent размещаются в следующих каталогах:

| Файлы | Location |
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

## <a name="desired-state-configuration"></a>Настройка требуемого состояния

Чтобы развернуть Dependency Agent с помощью Desired State Configuration, можно использовать модуль xPSDesiredStateConfiguration со следующим примером кода.

```powershell
configuration ServiceMap {

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

Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* Путем скачивания и запуска скрипта PowerShell, доступного из [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Развертывание Azure Monitor для виртуальных машин

В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не умеете развертывать ресурсы с помощью шаблона, см. следующие материалы:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Чтобы использовать Azure CLI, сначала необходимо установить и использовать CLI локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Сведения об установке или обновлении Azure CLI см. в разделе [установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
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

    Для завершения изменения конфигурации может потребоваться несколько минут. По завершении появится сообщение, похожее на следующее и содержащее результат:

    ```powershell
    provisioningState       : Succeeded
    ```
   После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть сведения о состоянии работоспособности и метрики для гибридного компьютера.

## <a name="troubleshooting"></a>Устранение неисправностей

### <a name="vm-doesnt-appear-on-the-map"></a>Виртуальная машина не отображается на карте

Если установка агента зависимостей прошла удачно, но компьютер не отображается на карте, определите проблему, выполнив следующие действия.

1. Успешно ли установлен Dependency Agent? Для этого проверьте, установлена и запущена ли служба.

    **Windows**: найдите службу с именем "Microsoft Dependency Agent". 

    **Linux**: найдите выполняющийся процесс microsoft-dependency-agent.

2. Вы в ценовой категории " [бесплатный" log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Бесплатный план позволяет создавать до пяти уникальных компьютеров. Все последующие компьютеры не будут отображаться на карте, даже если предыдущие пять больше не отправляют данные.

3. Отправляет ли компьютер журнал и данные о производительности в Azure Monitor журналы? Выполните следующий запрос к компьютеру: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Возвращает ли он один или несколько результатов? Это последние данные? Если это так, то агент Log Analytics работает правильно и взаимодействует со службой. Если это не так, проверьте агент на сервере. Дополнительные сведения см. в разделах [Устранение неполадок ориентирования Management Suite операций](../platform/agent-windows-troubleshoot.md) или [Устранение неполадок с агентом Azure Log Analytics для Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Компьютер отображается на карте, но не содержит процессов

Если сервер отображается на карте, но у него нет данных процесса или соединения, это означает, что агент зависимостей установлен и работает, но драйвер ядра не был загружен. 

Проверьте файл C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) или /var/opt/microsoft/dependency-agent/log/service.log (Linux). В последних строках файла должно быть указано, почему не удалось загрузить ядро. Например, если вы обновили ядро, оно может не поддерживаться в Linux.


## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда наблюдение включено для виртуальных машин, эти сведения доступны для анализа с Azure Monitor для виртуальных машин.
 
- Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md).

- Чтобы определить узкие места и общее использование с производительностью виртуальной машины, см. статью [Просмотр производительности виртуальной машины Azure](vminsights-performance.md).
