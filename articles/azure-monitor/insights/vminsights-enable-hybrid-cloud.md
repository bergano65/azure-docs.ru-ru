---
title: Включить в гибридной среде Azure Monitor (Предварительная версия) | Документация Майкрософт
description: В этой статье описывается, как включить Azure Monitor для виртуальных машин для гибридной облачной среды, которая содержит один или несколько виртуальных машин.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 6b8870f0a6f14536fdf3a1ff675f2fbe3ce8aeec
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524195"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Включить монитор Azure для виртуальных машин (Предварительная версия) в гибридной среде

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

В этой статье объясняется, как включить Azure Monitor для виртуальных машин (Предварительная версия) для виртуальных машин или физических компьютеров, размещенных в вашем центре обработки данных или другой облачной среде. В конце этого процесса, который будет успешно начала мониторинга виртуальных машин в вашей среде и узнайте, если они возникли проблемы производительности или доступности. 

Прежде чем Приступая к работе, обязательно просмотрите [предварительные требования](vminsights-enable-overview.md) и убедитесь, ваша подписка и ресурсы отвечают требованиям. Ознакомьтесь с требованиями и методами развертывания для [агента Log Analytics в Linux и Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Dependency Agent в Azure Monitor для виртуальных машин не передает данные и не требует изменений в настройках брандмауэра или портов. Данные сопоставления всегда передаются агентом Log Analytics в службу Azure Monitor либо непосредственно, либо через [шлюз OMS](../../azure-monitor/platform/gateway.md), если политики безопасности ИТ не позволяют компьютерам в сети подключаться к Интернету.

Задачи для выполнения этой задачи можно обобщить следующим образом:

1. Установите агент Log Analytics для Windows или Linux.

2. Скачайте и установите Dependency Agent схемы Azure Monitor для виртуальных машин [Windows](https://aka.ms/dependencyagentwindows) или [Linux](https://aka.ms/dependencyagentlinux).

3. Включите сбор данных счетчиков производительности.

4. Разверните Azure Monitor для виртуальных машин.

## <a name="install-the-dependency-agent-on-windows"></a>Установка Dependency Agent на Windows
Dependency Agent можно вручную установить на компьютерах Windows с помощью `InstallDependencyAgent-Windows.exe`. Если запустить этот исполняемый файл без параметров, запустится мастер для установки агента в интерактивном режиме.

>[!NOTE]
>Для установки или удаления агента требуются права *администратора*.

В приведенной ниже таблице выделены параметры, поддерживаемые при установке агента из командной строки.

| Параметр | Описание |
|:--|:--|
| /? | Возвращает список параметров командной строки. |
| /S | Автоматическая установка без взаимодействия с пользователем. |

Например, чтобы запустить программу установки с параметром `/?`, введите **InstallDependencyAgent-Windows.exe /?**.

Файлы для Windows Dependency Agent по умолчанию устанавливаются в папку *C:\Program Files\Microsoft Dependency Agent*. Если Dependency Agent не запускается после завершения установки, просмотрите подробные сведения об ошибке в записях журналов. Каталогом журналов является *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Установка Dependency Agent в Linux
Dependency Agent устанавливается на серверах Linux с помощью файла *InstallDependencyAgent-Linux64.bin*. Это сценарий оболочки с самоизвлекающимся двоичным файлом. Вы можете запустить этот файл с помощью `sh` или добавить разрешения на выполнение в сам файл.

>[!NOTE]
> Для установки или настройки агента требуется доступ с правами привилегированного пользователя.
>

| Параметр | Описание |
|:--|:--|
| -help | Получение списка параметров командной строки. |
| -s | Выполняет автоматическую установку без вывода сообщений для пользователя. |
| --check | Проверка разрешений и операционной системы без установки агента. |

Например, чтобы запустить программу установки с параметром `-help`, введите **InstallDependencyAgent-Linux64.bin -help**.

Установите Dependency Agent для Linux с правами привилегированного пользователя. Для этого выполните команду `sh InstallDependencyAgent-Linux64.bin`.

Если Dependency Agent не запускается, просмотрите подробные сведения об ошибке в записях журналов. В агентах Linux каталог журнала находится в расположении */var/opt/microsoft/dependency-agent/log*.

Файлы для Dependency Agent размещаются в следующих каталогах:

| Файлы | Расположение |
|:--|:--|
| Основные файлы | /opt/microsoft/dependency-agent |
| Файлы журналов | /var/opt/microsoft/dependency-agent/log |
| Файлы конфигурации | /etc/opt/microsoft/dependency-agent/config |
| Исполняемые файлы службы | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Двоичные файлы хранилища | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Включение счетчиков производительности
Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их нужно будет включить отдельно. Это можно сделать одним из двух способов:
* вручную, как описано в статье [Источники данных о производительности Windows и Linux в Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md);
* загрузив и запустив сценарий PowerShell, доступный в [коллекции Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).

## <a name="deploy-azure-monitor-for-vms"></a>Развертывание Azure Monitor для виртуальных машин
В этом методе используется шаблон JSON, который задает конфигурацию для включения компонентов решения в рабочей области Log Analytics.

Если вы не знакомы с развертыванием ресурсов с помощью шаблона, см. статьи:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
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

    Изменение конфигурации может занять несколько минут. После настройки появится сообщение, похожее на приведенное ниже, с таким результатом:

    ```powershell
    provisioningState       : Succeeded
    ```
   После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть сведения о состоянии работоспособности и метрики для гибридного компьютера.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда мониторинг включен для виртуальных машин, эти сведения можно получить для анализа с помощью Azure Monitor для виртуальных машин. Чтобы узнать, как использовать функцию работоспособности, см. статью о [просмотре сведений о работоспособности Azure Monitor для виртуальных машин](vminsights-health.md). Для просмотра обнаруженных зависимостей приложений см. статью о [просмотре схемы Azure Monitor для виртуальных машин](vminsights-maps.md). Чтобы выявить узкие места производительности и общий уровень загрузки для виртуальных машин, изучите статью о [просмотре данных производительности виртуальной машины Azure](vminsights-performance.md). Просмотреть обнаруженные зависимости приложений поможет статья о [функции карты Azure Monitor для виртуальных машин](vminsights-maps.md).