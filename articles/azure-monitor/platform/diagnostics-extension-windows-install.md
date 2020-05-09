---
title: Установка и настройка расширения системы диагностики Microsoft Azure (WAD)
description: Узнайте, как получить данные диагностики Azure в учетной записи хранения Azure, чтобы их можно было просматривать с помощью одного из нескольких доступных средств.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: dd18fd484ac456f0c38cd6d9b73a2395a08ad5d0
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883113"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Установка и настройка расширения системы диагностики Microsoft Azure (WAD)
Расширение системы диагностики Azure — это агент в Azure Monitor, собирающий данные мониторинга из операционной системы на виртуальной машине и рабочих нагрузок виртуальных машин Azure и других ресурсов вычислений. В этой статье приводятся сведения об установке и настройке расширения системы диагностики Windows, а также описание того, как данные хранятся в учетной записи хранения и Azure.

Расширение системы диагностики реализуется как [расширение виртуальной машины](../../virtual-machines/extensions/overview.md) в Azure, поэтому оно поддерживает те же параметры установки с помощью шаблонов диспетчер ресурсов, POWERSHELL и CLI. Дополнительные сведения об установке и обслуживании расширений виртуальных машин см. в разделе [расширения и компоненты виртуальной машины для Windows](../../virtual-machines/extensions/features-windows.md) .

## <a name="install-with-azure-portal"></a>Установка с помощью портал Azure
Вы можете установить и настроить расширение системы диагностики на отдельной виртуальной машине в портал Azure, которая предоставляет интерфейс, а не напрямую работать с конфигурацией. При включении расширения диагностики будет автоматически использоваться конфигурация по умолчанию с самыми распространенными счетчиками производительности и событиями. Эту конфигурацию по умолчанию можно изменить в соответствии с конкретными требованиями.

> [!NOTE]
> Существуют параметры расширения диагностики, которые нельзя настроить с помощью портал Azure, включая отправку данных в концентраторы событий Azure. Для этих параметров необходимо использовать один из других методов настройки.

1. Откройте меню виртуальной машины в портал Azure.
2. Щелкните **параметры диагностики** в разделе **мониторинг** меню Виртуальная машина.
3. Щелкните **включить мониторинг на гостевом уровне** , если расширение системы диагностики еще не включено.
4. Для виртуальной машины будет создана новая учетная запись хранения Azure с именем, которое будет основываться на имени группы ресурсов для виртуальной машины. Вы можете подключить виртуальную машину к другой учетной записи хранения, выбрав вкладку **Агент** .

![Параметры диагностики](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Конфигурацию по умолчанию можно изменить после включения расширения диагностики. В следующей таблице описаны параметры, которые можно изменить на разных вкладках. Некоторые параметры имеют **пользовательскую** команду, которая позволяет указать более подробную конфигурацию. Дополнительные сведения о различных параметрах см. в статье [схема расширения диагностики Windows](diagnostics-extension-schema-windows.md) .

| Вкладка | Описание |
|:---|:---|
| Обзор | Отображает текущую конфигурацию со ссылками на другие вкладки. |
| Счетчики производительности | Выберите счетчики производительности для накопления и частоту выборки для каждого из них.  |
| Журналы | Выберите данные журнала для собраний. К ним относятся журналы событий Windows, журналы IIS, журналы приложений .NET и события ETW.  |
| Аварийные дампы | Включите аварийный дамп для различных процессов. |
| Приемники | Включите приемники данных для отправки данных в назначения в дополнение к службе хранилища Azure.<br>Azure Monitor — отправляет данные о производительности в метрики Azure Monitor.<br>Application Insights — отправка данных в приложение Application Insights. |
| Агент | Измените следующую конфигурацию агента:<br>— Измените учетную запись хранения.<br>— Укажите максимальный локальный диск, используемый агентом.<br>— Настройте журналы для работоспособности самого агента.|


> [!NOTE]
> Хотя конфигурация для расширения диагностики может быть отформатирована в формате JSON или XML, любая конфигурация, выполненная в портал Azure, всегда будет храниться в формате JSON. Если вы используете XML с другим методом конфигурации и затем изменяете конфигурацию с помощью портал Azure, параметры будут заменены на JSON.

## <a name="resource-manager-template"></a>Шаблон Resource Manager
См. статью [Использование мониторинга и диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) в статье Развертывание расширения диагностики с помощью шаблонов Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI
Azure CLI можно использовать для развертывания расширения система диагностики Azure на существующей виртуальной машине с помощью команды [AZ VM Extension Set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) , как показано в следующем примере. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Защищенные параметры определяются в [элементе PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) схемы конфигурации. Ниже приведен минимальный пример файла защищенных параметров, который определяет учетную запись хранения. Подробные сведения о частных параметрах см. в разделе [Пример конфигурации](diagnostics-extension-schema-windows.md#privateconfig-element) .

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Общедоступные параметры определяются в [открытом элементе](diagnostics-extension-schema-windows.md#publicconfig-element) схемы конфигурации. Ниже приведен минимальный пример файла общедоступных параметров, который включает сбор журналов инфраструктуры диагностики, один счетчик производительности и один журнал событий. Подробные сведения о общедоступных параметрах см. в разделе [Пример конфигурации](diagnostics-extension-schema-windows.md#publicconfig-element) .

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell
PowerShell можно использовать для развертывания расширения система диагностики Azure на существующей виртуальной машине с помощью [Set-азвмдиагностиксекстенсион](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , как показано в следующем примере. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Частные параметры определяются в [элементе PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), а общедоступные параметры определяются в [открытом элементе](diagnostics-extension-schema-windows.md#publicconfig-element) схемы конфигурации. Вы также можете указать сведения об учетной записи хранения в качестве параметров командлета Set-Азвмдиагностиксекстенсион, а не включать их в закрытые параметры.

Ниже приведен минимальный пример файла конфигурации, который включает сбор журналов инфраструктуры диагностики, один счетчик производительности и один журнал событий. Подробные сведения о частных и общедоступных параметрах см. в разделе [Пример конфигурации](diagnostics-extension-schema-windows.md#publicconfig-element) . 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

См. также [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Хранилище данных
В следующей таблице перечислены различные типы данных, собираемых из расширения диагностики, а также хранятся ли они в виде таблицы или большого двоичного объекта. Данные, хранящиеся в таблицах, также могут храниться в больших двоичных объектах в зависимости от [параметра StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) в общедоступной конфигурации.


| Данные  | Тип хранилища | Описание |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Таблица | Изменения в мониторе диагностики и конфигурации. |
| ваддиректориестабле | Таблица | Каталоги, отслеживаемые монитором диагностики.  Сюда входят журналы IIS, журналы неудачно завершенных запросов IIS и пользовательские папки.  Расположение файла журнала BLOB-объекта указано в поле Container, а имя большого двоичного объекта — в поле RelativePath.  В поле AbsolutePath указано расположение и имя файла на виртуальной машине Azure. |
| WadLogsTable | Таблица | Журналы, написанные в коде с помощью прослушивателя трассировки. |
| WADPerformanceCountersTable | Таблица | Счетчики производительности. |
| WADWindowsEventLogsTable | Таблица | журналы событий Windows; |
| WAD-IIS-фаиледреклогфилес | BLOB-объект | Содержит сведения из журналов неудачных запросов IIS. |
| wad-iis-logfiles | BLOB-объект | Содержит сведения о журналах IIS. |
| настройки | BLOB-объект | Пользовательский контейнер, основанный на настройке каталогов, отслеживаемых монитором диагностики.  Имя этого контейнера BLOB-объектов указывается в элементе WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Средства для просмотра диагностических данных
Данные после их передачи в хранилище можно просмотреть с помощью нескольких средств. Пример:

* Обозреватель сервера в Visual Studio. Если установлены инструменты Azure для Microsoft Visual Studio, можно использовать узел хранилища Azure в обозревателе сервера для просмотра больших двоичных объектов, доступных только для чтения, и табличных данных из учетных записей хранения Azure. Вы можете отображать данные из своей локальной учетной записи эмулятора хранения, а также из учетных записей хранения, созданных для Azure. Дополнительные сведения см. в статье [Обзор ресурсов хранилища с помощью обозревателя сервера и управление ими](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Обозреватель службы хранилища Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) — это автономное приложение, которое упрощает работу с данными из службы хранилища Azure на платформе Windows, OSX и Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) содержит диспетчер диагностики Azure, который позволяет просматривать и скачивать данные диагностики, которые собирают выполняемые в Azure приложения, а также управлять этими данными.

## <a name="next-steps"></a>Next Steps
- Дополнительные сведения о пересылке данных мониторинга в концентраторы событий Azure см. в статье [Отправка данных из расширения системы диагностики Windows Azure в концентраторы событий](diagnostics-extension-stream-event-hubs.md) .
