---
title: Клиентские библиотеки Azure Analysis Services Документы Майкрософт
description: Описываются клиентские библиотеки, необходимые для подключения клиентских приложений и средств к службам Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5914c7987d5a54a6bcc779231287309517f5121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129222"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Библиотеки клиентов для подключения к аналитическим службам

Клиентские библиотеки требуются клиентским приложениям и средствам для подключения к серверам служб Analysis Services. Клиентские приложения Microsoft, такие как Power BI Desktop, Excel, S'L Server Management Studio (SSMS), а также расширение аналитических служб для Visual Studio установить все три клиентские библиотеки и обновить их вместе с регулярными обновлениями приложений. В некоторых случаях может потребоваться установка новых версий клиентских библиотек. Пользовательские клиентские приложения также требуют установки клиентских библиотек.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Скачивание последних версий клиентских библиотек (установщик Windows)  

|Скачивание  |Версия продукта  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.29.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.29.25       |
|[Объекты AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.8.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.8.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO и ADOMD (пакеты NuGet)

Объекты управления аналитическими службами (AMO) и клиентские библиотеки ADOMD доступны в виде установить пакеты из [NuGet.org.](https://www.nuget.org/) Рекомендуется переходить на ссылки NuGet вместо использования Установки Windows. 

|Пакет  | Версия продукта  | 
|---------|---------|
|[Объекты AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.8.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.8.0      |

Сборки пакетов NuGet AssemblyVersion следуют семантической организации номера версии — "Главная.Второстепенная.Исправление". Ссылки на пакеты NuGet загружают ожидаемую версию, даже при наличии другой версии в глобальном кэше сборок (в результате установки MSI). Номер исправления увеличивается с каждым выпуском. Версии ADOMD и AMO синхронизируются.

## <a name="understanding-client-libraries"></a>Основные сведения о клиентских библиотеках

Службы Analysis Services используют три типа клиентских библиотек, именуемых также поставщиками данных. ADOMD.NET и объекты AMO (объекты управления Analysis Services) — это управляемые клиентские библиотеки. Поставщик Analysis Services OLE DB (MSOLAP DLL) — это собственная клиентская библиотека. Как правило, все три библиотеки устанавливаются одновременно. **Службы Azure Analysis Services поддерживают только последние версии всех трех библиотек**. 

Клиентские приложения Майкрософт, в частности Power BI Desktop и Excel, устанавливают все три клиентские библиотеки и обновляют их незамедлительно при выходе новых версий. Но в зависимости от версии или частоты обновлений могут устанавливаться устаревшие, а не новые версии клиентских библиотек, необходимые для служб Azure Analysis Services. Это же касается и пользовательских приложений или других интерфейсов, таких как AsCmd, TOM, ADOMD.NET. Для этих приложений клиентские библиотеки требуется устанавливать вручную или программным способом. Клиентские библиотеки, устанавливаемые вручную, включены в пакеты дополнительных компонентов SQL Server в качестве распространяемых пакетов. Однако они зависят от версии SQL Server, поэтому версия в пакетах может быть не последней.  

Клиентские библиотеки для клиентских подключений отличаются от поставщиков данных, требуемых для подключения с сервера служб Azure Analysis Services к источнику данных. Чтобы узнать больше о [Data source connections](analysis-services-datasource.md)соединениях источника данных, см.

## <a name="client-library-types"></a>Типы клиентских библиотек

### <a name="analysis-services-ole-db-provider-msolap"></a>Поставщик OLE DB службы Analysis Services (MSOLAP) 

 Поставщик Analysis Services OLE DB (MSOLAP) представляет собой собственную клиентскую библиотеку для подключения к базе данных служб Analysis Services. Он косвенно используется в ADOMD.NET и объектах AMO, делегируя запросы соединений поставщику данных. Также вы можете вызывать поставщик OLE DB напрямую из кода приложения.  
  
 Поставщик Analysis Services OLE DB устанавливается автоматически большинством средств и клиентских приложений, которые используются для доступа к базам данных служб Analysis Services. Его следует установить на всех компьютерах, с которых выполняется доступ к данным Analysis Services.  
  
 Поставщики OLE DB часто указываются в строках подключения. В строке подключения служб Analysis Services используется другой формат указания поставщика OLE DB: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO — это управляемая клиентская библиотека, которая используется для администрирования сервера и определения данных. Она устанавливается и используется средствами и клиентскими приложениями. Например, SQL Server Management Studio (SSMS) использует AMO для подключения к службам Analysis Services. Подключение с помощью объектов AMO предельно просто. Достаточно лишь указать `"data source=\<servername>"`. Когда подключение будет установлено, этот API можно использовать для работы с коллекциями баз данных и основными объектами. Visual Studio и SSMS используют AMO для подключения к экземпляру аналитических служб.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET — это управляемая клиентская библиотека, используемая для запросов к данным Analysis Services. Она устанавливается и используется средствами и клиентскими приложениями. 
  
 Во всех трех библиотеках используются похожие свойства строк подключения к базе данных. Практически любая строка подключения, созданная для ADOMD.NET с помощью [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString), подойдет и для объектов AMO и (или) поставщика Analysis Services OLE DB (MSOLAP). Дополнительные сведения см. в статье [Свойства строки подключения (службы Analysis Services)](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Как определить версию клиентской библиотеки   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Перейдите к `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Если здесь несколько папок, выберите папку с самым большим номером.
  
2.  Право нажмите **msolap.dll** > **Свойства** > **Подробная информация**. Если файл называется msolap140.dll, это более ранняя версия. Его необходимо обновить.
    
    ![Сведения о клиентской библиотеке](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Перейдите к `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Если здесь несколько папок, выберите папку с самым большим номером.
2. Право нажмите **Microsoft.AnalysisServices** > **Свойства** > **Подробная информация**.  

### <a name="adomd"></a>ADOMD

1. Перейдите к `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Если здесь несколько папок, выберите папку с самым большим номером.
2. Право нажмите **Microsoft.AnalysisServices.AdomdClient** > **Свойства** > **Подробная информация**.  


## <a name="next-steps"></a>Дальнейшие действия
[Связь с Excel](analysis-services-connect-excel.md)    
[Подключение с помощью Power BI](analysis-services-connect-pbi.md)
