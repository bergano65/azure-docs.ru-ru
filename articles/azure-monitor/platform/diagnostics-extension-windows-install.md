---
title: Установка и настройка расширения диагностики Windows Azure (WAD)
description: Узнайте, как собирать данные диагностики Azure в учетной записи хранения Azure, чтобы просмотреть их с помощью одного из нескольких доступных инструментов.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672265"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Установка и настройка расширения диагностики Windows Azure (WAD)
Расширение azure diagnostics является агентом Azure Monitor, который собирает данные мониторинга с гостевой операционной системы и рабочие нагрузки виртуальных машин Azure и других вычислительных ресурсов. В этой статье приводится подробная информация об установке и настройке расширения диагностики Windows и описание того, как хранятся данные в учетной записи хранилища Azure.

Расширение диагностики реализовано как [виртуальное расширение машины](../../virtual-machines/extensions/overview.md) в Azure, поэтому оно поддерживает те же параметры установки, используя шаблоны Resource Manager, PowerShell и CLI. Смотрите [виртуальные расширения машины и функции для Windows](../../virtual-machines/extensions/features-windows.md) для получения подробной информации об установке и обслуживании виртуальных расширений машины.

## <a name="install-with-azure-portal"></a>Установка на порталaz
Вы можете установить и настроить расширение диагностики на отдельной виртуальной машине на портале Azure, который предоставляет вам интерфейс, а не работать непосредственно с конфигурацией. При включении расширения диагностики он автоматически использует конфигурацию по умолчанию с наиболее распространенными счетчиками производительности и событиями. Эту конфигурацию по умолчанию можно изменить в соответствии с определенными требованиями.

> [!NOTE]
> Есть параметры расширения диагностики, которые нельзя настроить с помощью портала Azure, включая отправку данных в концентраторы событий Azure. Для этих настроек необходимо использовать один из других методов конфигурации.

1. Откройте меню для виртуальной машины на портале Azure.
2. Нажмите на **настройки диагностики** в разделе **Мониторинг** меню VM.
3. Нажмите **«Включить гостевой мониторинг,** если расширение диагностики еще не включено.
4. Для VM будет создана новая учетная запись хранения Azure с именем, на основе имени группы ресурсов для VM. Вы можете прикрепить VM к другой учетной записи хранения, выбрав вкладку **Агента.**

![Параметры диагностики](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Можно изменить конфигурацию по умолчанию после включения расширения диагностики. В следующей таблице описаны параметры, которые можно изменить в различных вкладок. Некоторые параметры имеют **пользовательскую** команду, которая позволяет указать более подробную конфигурацию; см. [схему расширения диагностики Windows](diagnostics-extension-schema-windows.md) для получения подробной информации о различных настройках.

| Вкладка | Описание |
|:---|:---|
| Обзор | Отображает текущую конфигурацию со ссылками на другие вкладки. |
| Счетчики производительности | Выберите счетчики производительности для сбора и частоту выборки для каждого из них.  |
| Журналы | Выберите данные журнала для сбора. Это включает журналы событий Windows, журналы IIS, журналы приложений .NET и события ETW.  |
| Аварийные дампы | Включить сбой дампа для различных процессов. |
| Приемники | Позволяет поглотителям данных отправлять данные в пункты назначения в дополнение к хранилищам Azure.<br>Azure Monitor - Отправка данных о производительности в метеокazmonitor.<br>Application Insights - Отправить данные в приложение Application Insights. |
| Агент | Измените следующую конфигурацию для агента:<br>- Измените учетную запись хранилища.<br>- Укажите максимальный локальный диск, используемый для агента.<br>- Настройка журналов для здоровья самого агента.|


> [!NOTE]
> В то время как конфигурация для расширения диагностики может быть отформатирована либо в JSON, либо в XML, любая конфигурация, выполненная на портале Azure, всегда будет храниться как JSON. Если вы используете XML с другим методом конфигурации, а затем измените конфигурацию с порталом Azure, настройки будут изменены на JSON.

## <a name="resource-manager-template"></a>Шаблон Resource Manager
Смотрите [использование мониторинга и диагностики с шаблонами Управления ресурсами Windows VM и Azure](../../virtual-machines/extensions/diagnostics-template.md) о развертывании расширения диагностики с шаблонами Управления ресурсами Azure. 

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI
CLI Azure можно использовать для развертывания расширения Azure Diagnostics в существующую виртуальную машину с помощью [набора расширения az vm,](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) как в следующем примере. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Защищенные настройки определяются в [элементе PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) схемы конфигурации. Ниже приводится минимальный пример защищенного файла настроек, определяющего учетную запись хранилища. Смотрите [конфигурацию Пример](diagnostics-extension-schema-windows.md#privateconfig-element) для получения подробной информации о закрытых настройках.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Общедоступные настройки определяются в [общедоступном элементе](diagnostics-extension-schema-windows.md#publicconfig-element) схемы конфигурации. Ниже приводится минимальный пример файла общедоступных настроек, который позволяет собирать журналы диагностической инфраструктуры, единый счетчик производительности и единый журнал событий. Смотрите [конфигурацию Пример](diagnostics-extension-schema-windows.md#publicconfig-element) для получения полной информации о настройках общедоступных.

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
PowerShell можно использовать для развертывания расширения Azure Diagnostics на существующей виртуальной машине с помощью [Set-AzVMDiagnosticsExtension,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) как в следующем примере. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Частные настройки определяются в [элементе PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)в то время как общедоступные настройки определяются в [общедоступном элементе](diagnostics-extension-schema-windows.md#publicconfig-element) схемы конфигурации. Вы также можете указать детали учетной записи хранилища в качестве параметров cmdlet Set-AzVMDiagnosticsExtension, а не включать их в частные настройки.

Ниже приводится минимальный пример файла конфигурации, который позволяет собирать журналы диагностической инфраструктуры, единый счетчик производительности и один журнал событий. См [Пример конфигурации](diagnostics-extension-schema-windows.md#publicconfig-element) для получения полной информации о частных и общедоступных настройках. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
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
В следующей таблице перечислены различные типы данных, собранных из расширения диагностики, и хранятся ли они в виде таблицы или капли. Данные, хранящиеся в таблицах, также могут храниться в каплях в зависимости от [настройки StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) в общедоступной конфигурации.


| Данные | Тип хранилища | Описание |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Таблица | Диагностический монитор и изменения конфигурации. |
| WADDirectoriesTable | Таблица | Каталоги, которые диагностический монитор контролирует.  Сюда входят журналы IIS, журналы неудачно завершенных запросов IIS и пользовательские папки.  Расположение файла журнала BLOB-объекта указано в поле Container, а имя большого двоичного объекта — в поле RelativePath.  В поле AbsolutePath указано расположение и имя файла на виртуальной машине Azure. |
| WadLogsTable | Таблица | Логи, написанные в коде с помощью слушателя трассировки. |
| WADPerformanceCountersTable | Таблица | Счетчики производительности. |
| WADWindowsEventLogsTable | Таблица | журналы событий Windows; |
| Wad-iis-failedreqlog | BLOB-объект | Содержит информацию из журналов IIS Failed Request. |
| wad-iis-logfiles | BLOB-объект | Содержит информацию о журналах IIS. |
| "обычай" | BLOB-объект | Пользовательский контейнер, основанный на настройке каталогов, которые контролируются диагностическим монитором.  Имя этого контейнера BLOB-объектов указывается в элементе WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Средства для просмотра диагностических данных
Данные после их передачи в хранилище можно просмотреть с помощью нескольких средств. Пример:

* Обозреватель сервера в Visual Studio. Если установлены инструменты Azure для Microsoft Visual Studio, можно использовать узел хранилища Azure в обозревателе сервера для просмотра больших двоичных объектов, доступных только для чтения, и табличных данных из учетных записей хранения Azure. Вы можете отображать данные из своей локальной учетной записи эмулятора хранения, а также из учетных записей хранения, созданных для Azure. Дополнительные сведения см. в статье [Обзор ресурсов хранилища с помощью обозревателя сервера и управление ими](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Обозреватель службы хранилища Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) — это автономное приложение, которое упрощает работу с данными из службы хранилища Azure на платформе Windows, OSX и Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) содержит диспетчер диагностики Azure, который позволяет просматривать и скачивать данные диагностики, которые собирают выполняемые в Azure приложения, а также управлять этими данными.

## <a name="next-steps"></a>Next Steps
- [См. Отправку данных из расширения диагностики Windows Azure в центры событий](diagnostics-extension-stream-event-hubs.md) для получения подробной информации о пересылке данных мониторинга в концентраторы событий Azure.
