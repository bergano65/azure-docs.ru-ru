---
title: Отправка метрик гостевых ОС в хранилище метрик Azure Monitor для классических облачных служб
description: Отправка метрик гостевых ОС в хранилище метрик Azure Monitor для облачных служб
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7f10495e22cf6750fdc5891d760885a238175da8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711790"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Отправка метрик гостевых ОС в хранилище метрик Azure Monitor для классических облачных служб 

[Расширение диагностики](azure-diagnostics.md) для Azure Monitor позволяет собирать метрики и журналы из гостевой операционной системы (гостевой ОС), работающей на виртуальной машине, в облачной службе или в кластере Service Fabric. Это расширение может отправлять данные телеметрии во [множество различных расположений](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

В этой статье описывается процесс отправки метрик производительности гостевой ОС для классической облачной службы Azure в хранилище метрик Azure Monitor. Начиная с версии 1.11 расширение диагностики позволяет записывать метрики напрямую в хранилище метрик Azure Monitor, где уже собраны стандартные метрики платформы. 

Хранение их в этом расположении позволяет получить доступ к тем же действиям, которые доступны для метрик платформы. К этим действиям относятся оповещения практически в реальном времени, построение диаграмм, маршрутизация, доступ из REST API и многое другое.  Ранее расширение диагностики записывало данные в службу хранилища Azure, а не в хранилище данных Azure Monitor.  

Процесс, описанный в этой статье, выполняется только для счетчиков производительности в облачных службах Azure. Он не подходит для других пользовательских метрик. 

## <a name="prerequisites"></a>Предварительные требования

- Вам необходимы права [администратора службы или соадминистратора](~/articles/billing/billing-add-change-azure-subscription-administrator.md) в подписке Azure. 

- Подписку необходимо зарегистрировать в [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Необходимо установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) или [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="provision-a-cloud-service-and-storage-account"></a>Подготовка облачной службы и учетной записи хранения 

1. Создайте и разверните классическую облачную службу. Пример классического приложения для облачной службы и описание процесса его развертывания см. в статье [Начало работы с облачными службами Azure и ASP.NET](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Вы можете использовать существующую учетную запись хранения или развернуть новую. Учетную запись хранения рекомендуется создавать в том же регионе, где развернута созданная вами классическая облачная служба. На портале Azure перейдите в колонку ресурсов **Учетные записи хранения** и выберите **Ключи**. Запишите имя учетной записи хранения и ключ доступа к ней. Они понадобятся на последующих шагах.

   ![Ключи учетной записи хранения](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)

## <a name="create-a-service-principal"></a>Создание субъекта-службы 

Создайте субъект-службу для своего клиента Azure Active Directory согласно инструкциям в статье о [создании приложения и субъекта-службы Azure Active Directory с правами доступа к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Выполняя это действие, обратите внимание на следующие моменты: 

- Вы можете указать любой URL-адрес для входа в систему.  
- Создайте новый секрет клиента для этого приложения.  
- Сохраните ключ и идентификатор клиента, чтобы использовать их в дальнейшем.  

Предоставьте приложению, созданному на предыдущем шаге, права *издателя метрик мониторинга* для доступа к ресурсу, из которого вы хотите передавать метрики. Если вы планируете использовать приложение для генерации пользовательских метрик ко многим ресурсам, можно предоставить эти разрешения группе ресурсов или уровню подписки.  

> [!NOTE]
> Расширение диагностики будет использовать субъект-службу для проверки подлинности в Azure Monitor и передавать метрики для облачной службы.

## <a name="author-diagnostics-extension-configuration"></a>Создание конфигурации для расширения диагностики 

Подготовьте файл конфигурации для расширения диагностики. Этот файл определяет, какие журналы и счетчики производительности расширение диагностики должно собирать для облачной службы. Ниже приведен пример файла конфигурации диагностики:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

В разделе SinksConfig файла диагностики укажите новый приемник Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

В разделе файла конфигурации, где находится список собираемых счетчиков производительности, добавьте приемник Azure Monitor. Эта запись необходима для того, чтобы все указанные счетчики производительности направлялись в Azure Monitor в качестве метрик. Счетчики производительности можно добавлять или удалять в соответствии с потребностями. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Наконец в закрытой конфигурации добавьте раздел *Учетная запись Azure Monitor*. Укажите созданные ранее идентификатор клиента и секрет для субъекта-службы. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Сохраните этот файл диагностики локально.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Развертывание расширения диагностики в облачной службе 

Запустите PowerShell и войдите в Azure. 

```PowerShell
Login-AzureRmAccount 
```

Используйте следующие команды для сохранения сведений об учетной записи хранения, которая была создана ранее. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Аналогичным образом укажите путь к файлу диагностики в переменной с помощью следующей команды:

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Разверните расширение диагностики в облачной службе с помощью файла диагностики, содержащего настроенный приемник Azure Monitor, используя следующую команду:  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> В процессе установки расширения диагностики по-прежнему необходимо указывать учетную запись хранения. Все журналы и счетчики производительности, указанные в файле конфигурации диагностики, должны записываться в указанную учетную запись хранения.  

## <a name="plot-metrics-in-the-azure-portal"></a>График метрик на портале Azure 

1. Перейдите на портал Azure. 

   ![Метрики портала Azure](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

2. В меню слева выберите **Монитор**.

3. В колонке **Монитор** выберите вкладку **Metrics Preview** (Предварительный просмотр метрик).

4. В раскрывающемся меню ресурсов выберите требуемую классическую облачную службу.

5. В раскрывающемся меню пространств имен выберите **azure.vm.windows.guest**. 

6. В раскрывающемся списке метрик выберите **Использование выделенной памяти (в байтах)**. 

Можно использовать функции фильтрации и разделения по измерениям, чтобы просмотреть общий объем памяти, используемой определенной ролью или экземпляром роли. 

 ![Метрики портала Azure](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).
