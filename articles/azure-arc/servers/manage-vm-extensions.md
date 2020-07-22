---
title: Управление расширениями виртуальной машины с помощью дуги Azure для серверов
description: Служба "Дуга Azure для серверов (Предварительная версия)" может управлять развертыванием расширений виртуальных машин, которые обеспечивают настройку и задачи автоматизации после развертывания с помощью виртуальных машин, не относящихся к Azure.
ms.date: 06/17/2020
ms.topic: conceptual
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.openlocfilehash: e04b2c63f782a4fcbb64273c08511ecb0bbc2bc3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84977651"
---
# <a name="virtual-machine-extension-management-with-azure-arc-for-servers-preview"></a>Управление расширениями виртуальной машины с помощью дуги Azure для серверов (Предварительная версия)

Расширения виртуальных машин — это небольшие приложения, которые обеспечивают настройку и задачи автоматизации, выполняемые после развертывания на виртуальных машинах Azure. Например, с помощью расширения виртуальной машины (далее — расширение ВМ) на виртуальной машине можно установить программное обеспечение, настроить антивирусную защиту или средство для выполнения сценариев.

Служба "Дуга Azure для серверов (Предварительная версия)" позволяет развертывать расширения виртуальных машин Azure на виртуальных машинах Windows и Linux без Azure, упрощая управление гибридными компьютерами в локальной среде, пограничных и других облачных средах с помощью своего жизненного цикла.

## <a name="key-benefits"></a>Основные преимущества

Служба "Дуга Azure для серверов (Предварительная версия)" поддерживает следующие основные преимущества:

* Используйте [конфигурацию состояния службы автоматизации Azure](../../automation/automation-dsc-overview.md) для централизованного хранения конфигураций и поддержки требуемого состояния гибридных подключенных компьютеров, включенных с помощью расширения ВИРТУАЛЬНОЙ машины DSC.

* Собирайте данные журналов для анализа с помощью [журналов в Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) , включенных с помощью расширения виртуальной машины агента log Analytics. Это полезно для выполнения сложного анализа данных из различных источников.

* С помощью [Azure Monitor для виртуальных машин](../../azure-monitor/insights/vminsights-overview.md)анализирует производительность виртуальных машин Windows и Linux и отслеживает их процессы и зависимости от других ресурсов и внешних процессов. Это достигается благодаря включению как агента Log Analytics, так и расширений виртуальной машины агента зависимостей.

* Скачайте и выполните скрипты на гибридных подключенных компьютерах с помощью расширения пользовательских сценариев. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления.

## <a name="availability"></a>Доступность

Функциональность расширения виртуальной машины доступна только в списке [поддерживаемых регионов](overview.md#supported-regions). Убедитесь, что ваш компьютер подключен в одном из этих регионов.

## <a name="extensions"></a>Расширения

В этой предварительной версии мы поддерживаем следующие расширения виртуальных машин на компьютерах Windows и Linux.

|Расширение |Операционная система |Publisher |Дополнительные сведения |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute; |[Расширение пользовательских сценариев Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Расширение DSC Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Агент Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. extension |[Расширение пользовательских сценариев Linux версии 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Расширение PowerShell DSC для Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Агент Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Расширение виртуальной машины Log Analytics для Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute; | [Расширение виртуальной машины агента зависимостей для Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Расширения ВМ можно запускать с помощью шаблонов Azure Resource Manager, из портал Azure или Azure PowerShell на гибридных серверах, управляемых ARC для серверов (Предварительная версия).

Сведения о пакете агента подключенного компьютера Azure и сведения о компоненте агента расширения см. в разделе [Обзор агентов](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Предварительные условия

Эта функция зависит от следующих поставщиков ресурсов Azure в вашей подписке:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Если они еще не зарегистрированы, выполните действия, описанные в разделе [Регистрация поставщиков ресурсов Azure](agent-overview.md#register-azure-resource-providers).

Для расширения виртуальной машины агента Log Analytics для Linux требуется, чтобы на целевом компьютере был установлен Python 2. x.

### <a name="connected-machine-agent"></a>Агент подключенного компьютера

Убедитесь, что компьютер соответствует [поддерживаемым версиям](agent-overview.md#supported-operating-systems) операционной системы Windows и Linux для агента подключенного компьютера Azure.

Минимальная версия агента подключенного компьютера, поддерживаемая с помощью этой функции:

* Windows-0,7. x
* Linux-0,8. x

Сведения об обновлении компьютера до версии, необходимой для агента, см. в разделе [Upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Включение расширений с портала

Расширения виртуальной машины можно применить к компьютеру, управляемому на сервере (Предварительная версия), с помощью портал Azure.

1. В браузере перейдите на [портал Azure](https://aka.ms/arcserver-preview).

2. На портале перейдите к пункту **Компьютеры — дуга Azure** и выберите Гибридный компьютер из списка.

3. Выберите **расширения**, а затем щелкните **Добавить**. Выберите нужное расширение из списка доступных расширений и следуйте инструкциям мастера. В этом примере будет развернуто расширение виртуальной машины Log Analytics. 

    ![Выберите расширение виртуальной машины для выбранного компьютера](./media/manage-vm-extensions/add-vm-extensions.png)

    В следующем примере показано, как установить расширение Log Analytics VM из портал Azure.

    ![Установка расширения Log Analytics VM](./media/manage-vm-extensions/mma-extension-config.png)

    Чтобы завершить установку, необходимо указать идентификатор рабочей области и первичный ключ. Если вы не знакомы с поиском этих сведений, см. статью [Получение идентификатора и ключа рабочей области](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key).

4. После подтверждения требуемой информации выберите **создать**. Отобразится сводка развертывания, и вы сможете просмотреть состояние развертывания.

>[!NOTE]
>Хотя несколько расширений можно объединять и обрабатывать, они устанавливаются последовательно. После завершения установки первого расширения будет предпринята установка следующего расширения.

## <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Расширения ВМ можно добавлять в шаблоны Azure Resource Manager. Так они смогут выполняться при развертывании соответствующего шаблона. Используя расширения виртуальной машины, поддерживаемые ARC для серверов (Предварительная версия), можно развернуть поддерживаемое расширение виртуальной машины на компьютерах под управлением Linux или Windows с помощью Azure PowerShell. Каждый пример ниже включает файл шаблона и файл параметров с примерами значений, которые необходимо предоставить шаблону.

>[!NOTE]
>Хотя несколько расширений можно объединять и обрабатывать, они устанавливаются последовательно. После завершения установки первого расширения будет предпринята установка следующего расширения.

### <a name="deploy-the-log-analytics-vm-extension"></a>Развертывание расширения виртуальной машины Log Analytics

Для простого развертывания агента Log Analytics для установки агента в Windows или Linux предоставляется следующий пример.

#### <a name="template-file-for-linux"></a>Файл шаблона для Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Файл шаблона для Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Файл параметров

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Сохраните файлы шаблона и параметров на диске и измените файл параметров, указав соответствующие значения для развертывания. Затем можно установить расширение на всех подключенных компьютерах в группе ресурсов с помощью следующей команды. Команда использует параметр *TemplateFile* для указания шаблона, а параметр *TemplateParameterFile* — для указания файла, содержащего параметры и значения параметров.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Развертывание расширения пользовательских скриптов

Для использования расширения пользовательских сценариев в Windows и Linux предоставляется следующий пример. Если вы не знакомы с расширением пользовательских сценариев, см. раздел [расширение пользовательских сценариев для Windows](../../virtual-machines/extensions/custom-script-windows.md) или [расширение пользовательских скриптов для Linux](../../virtual-machines/extensions/custom-script-linux.md). Существует несколько различных характеристик, которые следует учитывать при использовании этого расширения с гибридными компьютерами:

* Список поддерживаемых операционных систем с расширением пользовательских сценариев виртуальной машины Azure неприменим к службе "Дуга" для серверов Azure. Список поддерживаемых ОС для ARC для серверов можно найти [здесь](agent-overview.md#supported-operating-systems).

* Сведения о конфигурации, касающиеся масштабируемых наборов виртуальных машин Azure или классических виртуальных машин, неприменимы.

* Если компьютерам требуется загрузить скрипт извне и он может взаимодействовать только через прокси-сервер, необходимо [настроить агент подключенного компьютера](manage-agent.md#update-or-remove-proxy-settings) , чтобы задать переменную среды прокси-сервера.

В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эта конфигурация указывается в шаблоне Azure Resource Manager, приведенном ниже для гибридных компьютеров Linux и Windows.

#### <a name="template-file-for-linux"></a>Файл шаблона для Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Файл шаблона для Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Развертывание расширения PowerShell DSC

Чтобы использовать расширение PowerShell DSC, используйте следующий пример для запуска в Windows и Linux. Если вы не знакомы с расширением PowerShell DSC, см. раздел [Общие сведения о обработчике расширений DSC](../../virtual-machines/extensions/dsc-overview.md). Существует несколько различных характеристик, которые следует учитывать при использовании этого расширения с гибридными компьютерами:

* Список поддерживаемых операционных систем с расширением DSC PowerShell для виртуальной машины Azure неприменим к службе "Дуга Azure" для серверов. Список поддерживаемых ОС для ARC для серверов можно найти [здесь](agent-overview.md#supported-operating-systems).

* Если компьютерам требуется загрузить скрипт извне и он может взаимодействовать только через прокси-сервер, необходимо [настроить агент подключенного компьютера](manage-agent.md#update-or-remove-proxy-settings) , чтобы задать переменную среды прокси-сервера.

#### <a name="template-file-for-linux"></a>Файл шаблона для Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Файл шаблона для Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Файл параметров

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Агент зависимостей

Чтобы использовать расширение агента зависимостей Azure Monitor, для работы в Windows и Linux предоставляется следующий пример. Если вы не знакомы с агентом зависимостей, см. раздел [Обзор агентов Azure Monitor](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Файл шаблона для Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Файл шаблона для Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>Устранение неполадок

Данные о состоянии развертываний расширений можно получить из портал Azure.

Следующие инструкции по устранению неполадок применимы ко всем расширениям виртуальных машин.

1. Чтобы проверить журнал гостевого агента, просмотрите действие при подготовке расширения в `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` для Windows и для Linux в `/var/lib/GuestConfig/ext_mgr_logs` .

2. Дополнительные сведения см. в журналах расширений для конкретного расширения `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . Выходные данные расширения записываются в файл для каждого расширения, установленного в Linux в `/var/log/GuestConfig/extension_logs` .

3. Дополнительные сведения о кодах ошибок, известных проблемах и т. д. см. в разделах документации по конкретным расширениям. Дополнительные сведения об устранении неполадок для каждого расширения можно найти в разделе **Устранение неполадок и поддержка** в обзоре расширения. Сюда входит описание кодов ошибок, записанных в журнал. Статьи расширения связаны в [таблице расширений](#extensions) , приведенной ранее в этой статье.

4. Просмотрите системные журналы. Проверьте наличие других операций, которые могут мешать работе расширения, например длительные установки других приложений, требующие монопольного доступа к диспетчеру пакетов.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять компьютером с помощью [Политики Azure](../../governance/policy/overview.md), например для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) и т. д.

- Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).