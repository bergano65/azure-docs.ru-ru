---
title: Среда выполнения интеграции в фабрике данных Azure
description: Сведения о среде выполнения интеграции в фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218813"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Среда выполнения интеграции в фабрике данных Azure
Среда выполнения интеграции (IR) — это инфраструктура вычислений, которую фабрика данных Azure использует для обеспечения перечисленных ниже возможностей интеграции данных в разных сетевых средах.

- **Data Flow**: Execute a [Data Flow](concepts-data-flow-overview.md) in managed Azure compute environment.  
- **Data movement**: Copy data across data stores in public network and data stores in private network (on-premises or virtual private network). Поддерживаются встроенные соединители, преобразование форматов, сопоставление столбцов, высокопроизводительная и масштабируемая передача данных.
- **Activity dispatch**:  Dispatch and monitor transformation activities running on a variety of compute services such as Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, and more.
- **Выполнение пакетов служб SSIS**. Выполнение пакетов служб SQL Server Integration Services (SSIS) в собственном коде в управляемой вычислительной среде Azure.

В фабрике данных действия определяют выполняемые операции. Связанная служба обозначает целевое хранилище данных или службу вычислений. Среда выполнения интеграции соединяет между собой действия и связанные службы.  It is referenced by the linked service or activity, and provides the compute environment where the activity either runs on or gets dispatched from. Такая схема позволяет выполнять действия в регионе, который максимально близко расположен к целевому хранилищу данных или службе вычислений, обеспечивает высокую производительность и соблюдение требований по безопасности и соответствию.

## <a name="integration-runtime-types"></a>Типы сред выполнения интеграции
Фабрика данных предлагает три типа сред выполнения интеграции. Вы можете выбрать тот, который отвечает вашим потребностям в интеграции данных и сетевой среде.  Эти три типа перечислены ниже.

- Azure
- Локальное размещение
- Службы SSIS Azure

В следующей таблице описаны возможности и поддержка сетей для каждого типа интеграции среды выполнения.

Тип IR | Общедоступная сеть | Частная сеть
------- | -------------- | ---------------
Azure | Поток данных<br/>Перемещение данных<br/>Диспетчеризация действий | &nbsp;
Локальное размещение | Перемещение данных<br/>Диспетчеризация действий | Перемещение данных<br/>Диспетчеризация действий
Службы SSIS Azure | Выполнение пакетов служб SSIS | Выполнение пакетов служб SSIS

На следующей схеме показано, как сочетать разные среды выполнения интеграции, чтобы обеспечить максимальные возможности по интеграции данных и поддержку сетей.

![Разные типы среды выполнения интеграции](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Среда выполнения интеграции Azure
В среде выполнения интеграции Azure доступны следующие возможности.

- Running Data Flows in Azure 
- Выполнение действия копирования между облачными хранилищами данных
- Dispatching the following transform activities in public network: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, and Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Сетевая среда Azure IR
Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. Для среды виртуальных сетей Azure следует использовать локально размещенную среду выполнения интеграции.

### <a name="azure-ir-compute-resource-and-scaling"></a>Вычислительные ресурсы и масштабирование Azure IR
Среда выполнения интеграции Azure предоставляет полностью управляемые вычисления в Azure без использования серверов.  Вам не нужно подготавливать инфраструктуру, устанавливать программное обеспечение, применять исправления и масштабировать емкость.  Кроме того, вы платите только за фактическое время использования.

Среда выполнения интеграции Azure предоставляет вычисления в собственном коде для безопасного, надежного и высокопроизводительного перемещения данных между облачными хранилищами данных.  Вам достаточно указать, сколько единиц интеграции данных нужно использовать для действия копирования, и среда Azure IR гибко увеличит объем вычислительных ресурсов соответствующим образом. Вам не нужно явно изменять размеры Integration Runtime. 

Диспетчеризация действий не требует больших ресурсов, поскольку лишь перенаправляет действие на целевую службу вычислений. В этом сценарии вам не нужно увеличивать объем вычислительных ресурсов.

Сведения о создании и настройке Azure IR см. в руководстве по созданию и настройке Azure IR. 

> [!NOTE] 
> Azure Integration runtime has properties related to Data Flow runtime, which defines the underlying compute infrastructure that would be used to run the data flows on. 

## <a name="self-hosted-integration-runtime"></a>Локальная среда IR
В локальной среде IR доступны следующие возможности.

- Выполнение действия копирования между облачными хранилищами данных и хранилищем данных в частной сети.
- Dispatching the following transform activities against compute resources in on-premises or Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activity, and Get Metadata activity.

> [!NOTE] 
> Use self-hosted integration runtime to support data stores that requires bring-your-own driver such as SAP Hana, MySQL, etc.  For more information, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) is a dependency of Self Hosted IR. Please make sure you have JRE installed on the same host.

### <a name="self-hosted-ir-network-environment"></a>Локальная сетевая среда IR
Если вы хотите безопасно выполнять интеграцию данных в среде частной сети, которая не связана напрямую с общедоступной облачной средой, вы можете установить локальную IR в локальной среде за корпоративным брандмауэром или внутри виртуальной частной сети.  Локальная среда выполнения интеграции использует только исходящие HTTP-соединения во внешнюю сеть.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Вычислительные ресурсы и масштабирование локальной среды IR
Локальная IR устанавливается на локальном компьютере или виртуальной машине в частной сети. В настоящее время локальная IR может выполняться только в операционной системе Windows.  

Чтобы получить высокий уровень доступности и масштабируемости, разверните локальную IR. Для этого следует связать логический экземпляр с несколькими локальными компьютерами в режиме "активный — активный".  For more information, see how to [create and configure self-hosted IR](create-self-hosted-integration-runtime.md) article under how to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Среда выполнения интеграции Azure SSIS
Чтобы переместить уже существующую рабочую нагрузку SSIS, создайте Azure SSIS IR для выполнения пакетов служб SSIS в собственной среде.

### <a name="azure-ssis-ir-network-environment"></a>Сетевая среда Azure-SSIS IR
Среда выполнения интеграции Azure SSIS может быть подготовлена в общедоступной сети или частной сети.  Доступ к локальным данным обеспечивается за счет присоединения среды выполнения интеграции Azure SSIS к виртуальной сети, подключенной к локальной сети.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Вычислительные ресурсы и масштабирование Azure-SSIS IR
Azure SSIS IR — это полностью управляемый кластер виртуальных машин Azure, выделенный для выполнения пакетов служб SSIS. Для размещения подключаемого каталога проектов и (или) пакетов служб SSIS (SSISDB) вы можете использовать собственную базу данных SQL Azure или сервер Управляемого экземпляра. Чтобы увеличить или уменьшить масштаб вычислительных ресурсов, укажите соответствующее количество узлов в кластере. Чтобы снизить расходы на среду выполнения интеграции служб SSIS Azure, вы можете в любой момент останавливать ее и снова запускать.

Дополнительные сведения см. в руководстве по созданию и настройке Azure SSIS IR.  После создания среды вы сможете развернуть существующие пакеты служб SSIS и управлять ими практически так же, как при использовании локальной SSIS, с помощью привычных средств, например SQL Server Data Tools (SSDT) и SQL Server Management Studio (SSMS).

Дополнительные сведения о среде выполнения Azure SSIS см. в следующих статьях: 

- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Создание среды выполнения интеграции Azure SSIS](create-azure-ssis-integration-runtime.md). Эта статья дополняет соответствующее руководство, а также предоставляет инструкции по использованию Управляемого экземпляра Базы данных SQL Azure и присоединению среды выполнения интеграции к виртуальной сети. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md). В этой статье показано, как остановить, запустить или удалить Azure SSIS IR. В ней также показано, как развернуть Azure SSIS IR путем добавления дополнительных узлов в среду выполнения интеграции. 
- [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md) В этой статье содержатся общие сведения о присоединении среды выполнения интеграции Azure–SSIS к виртуальной сети Azure. В ней также показано, как настроить виртуальную сеть, чтобы присоединить среду выполнения интеграции SSIS Azure к виртуальной сети с помощью портала Azure. 

## <a name="integration-runtime-location"></a>Расположение среды выполнения интеграции
Расположение фабрики данных — это место, в котором хранятся метаданные этой фабрики данных и из которого инициируется конвейер. Сейчас, чтобы переместить данные между хранилищами данных или обработать данные с помощью служб вычислений, фабрика данных может обращаться к хранилищам данных и службам вычислений в других регионах Azure. Это поведение реализуется через [глобально доступную среду IR](https://azure.microsoft.com/global-infrastructure/services/) для обеспечения соответствия данных, эффективности и снижения расходов на исходящий трафик.

Расположение среды IR определяет расположение ее вычислительных ресурсов, то есть, по сути, место выполнения всех операций по перемещению данных, диспетчеризации действий и выполнению пакета SSIS. Расположение IR может отличаться от расположения фабрики данных, которой она принадлежит. 

### <a name="azure-ir-location"></a>Расположение Azure IR
Вы можете задать определенное расположение Azure IR. При этом перемещение данных или диспетчеризация действий будет происходить в этом конкретном регионе. 

If you choose to use the **auto-resolve Azure IR** which is the default, 

- Для действия копирования в ADF прилагается максимум усилий, чтобы автоматически определить хранилище данных приемника и источника и выбрать наилучшее расположение — по возможности в том же регионе или ближайшем регионе в той же географической области. Если же не удается обнаружить такое расположение, как альтернатива используется регион фабрики данных.

- For Lookup/GetMetadata/Delete activity execution (also known as Pipeline activities), transformation activity dispatching (also known as External activities), and authoring operations (test connection, browse folder list and table list, preview data), ADF will use the IR in the data factory region.

- For Data Flow, ADF will use the IR in the data factory region. 

  > [!TIP] 
  > A good practice would be to ensure Data flow runs in the same region as your corresponding data stores (if possible). You can either achieve this by auto-resolve Azure IR (if data store location is same as Data Factory location), or by creating a new Azure IR instance in the same region as your data stores and then execute the data flow on it. 

Вы можете отслеживать, какие расположения IR используются во время выполнения действия, в представлении мониторинга действий конвейера в пользовательском интерфейсе или в полезных данных мониторинга действий.

>[!TIP]
>Если применяются строгие требования к соответствию данных и нужно, чтобы данные не покидали определенную географическую область, вы можете явным образом создать среду Azure IR в конкретном регионе и направить связанную службу в эту среду IR с помощью свойства ConnectVia. Например, если нужно скопировать данные из большого двоичного объекта в южной части Соединенного Королевства в хранилище данных SQL в южной части Соединенного Королевства и чтобы при этом данные не покидали Соединенного Королевства, создайте среду Azure IR в южной части Соединенного Королевства и добавьте ссылку на эту среду IR в обе связанные службы.

### <a name="self-hosted-ir-location"></a>Расположение локальной среды IR
Локальная IR логически регистрируется в фабрике данных, а вычислительные ресурсы для ее работы вы предоставляете самостоятельно. Поэтому для локальных IR нельзя явно задать свойство расположения. 

Если локальная IR используется для перемещения данных, она извлекает данные из источника и записывает их в приемник.

### <a name="azure-ssis-ir-location"></a>Расположение среды Azure-SSIS IR
От выбора расположения для службы Azure SSIS IR зависит производительность рабочих процессов извлечения, преобразования и загрузки.

- Расположение Azure SSIS IR может не совпадать с расположением фабрики данных, но должно совпадать с расположением сервера Базы данных SQL Azure или Управляемого экземпляра, на которых размещается база данных SSIS. Это позволит среде выполнения интеграции Azure SSIS легко обращаться к базе данных SSIS, не генерируя лишний трафик между расположениями.
- Если для размещения базы данных SSIS вы используете источники и назначения данных в локальной сети, а не существующий сервер Базы данных SQL Azure или Управляемого экземпляра, создайте новый сервер Базы данных SQL Azure или Управляемого экземпляра в том же расположении, где находится виртуальная сеть, подключенная к этой локальной сети.  Таким образом вы можете создать среду выполнения интеграции Azure SSIS, использующую новый сервер Базы данных SQL Azure или Управляемого экземпляра, и присоединить ее к виртуальной сети. Так как все эти ресурсы находятся в одном расположении, перемещение данных между разными расположениями будет минимальным.
- Иногда расположение существующего сервера Базы данных SQL Azure или Управляемого экземпляра, на котором размещается база данных SSIS, не совпадает с расположением виртуальной сети, подключенной к локальной сети. В этом случае сначала создайте среду выполнения интеграции Azure SSIS, использующую существующий сервер Базы данных SQL Azure или Управляемого экземпляра, и присоедините ее к другой виртуальной сети в том же расположении, а затем настройте подключение между виртуальными сетями из разных расположений.

На следующей схеме показаны параметры расположения фабрики данных и ее среды выполнения интеграции.

![Расположение среды выполнения интеграции](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Выбор IR для использования

### <a name="copy-activity"></a>Действие копирования

Для действия копирования нужны связанные службы источника и приемника данных, которые определяют направление потока данных. Далее описан алгоритм выбора экземпляра среды выполнения интеграции, который будет выполнять копирование. 

- **Копирование между двумя источниками данных в облаке.** Когда службы источника и приемника используют Azure IR, ADF будет использовать региональную среду Azure IR, если она указана. Либо же расположение Azure IR будет определяться автоматически, если вы выберете автоматическое разрешение IR (по умолчанию), как описано в разделе о [расположении среды выполнения интеграции](#integration-runtime-location).
- **Копирование между облачным источником данных и источником данных в частной сети**. Если связанная служба источника или приемника использует локальную IR, действие копирования выполняется в среде в этой локальной среде.
- **Копирование между двумя источниками данных в частной сети.** Обе связанные службы источника и приемника должны указывать на один экземпляр среды выполнения интеграции, и именно эта среда используется для выполнения действия копирования.

### <a name="lookup-and-getmetadata-activity"></a>Действие поиска и получения метаданных

Действие поиска и получения метаданных выполняется в среде выполнения интеграции, ассоциированной со связанной службой хранилища данных.

### <a name="transformation-activity"></a>Действие преобразования

Для каждого действия преобразования задается целевая связанная служба вычислений, которая указывает на определенную среду выполнения интеграции. Этот экземпляр среды выполнения интеграции будет отвечать за диспетчеризацию действия преобразования.

### <a name="data-flow-activity"></a>Data Flow activity

Data Flow activity is executed on the integration runtime associated to it. 

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [Create Azure integration runtime](create-azure-integration-runtime.md)
- [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md)
- [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md). Эта статья дополняет соответствующее руководство, а также предоставляет инструкции по использованию Управляемого экземпляра Базы данных SQL Azure и присоединению среды выполнения интеграции к виртуальной сети. 
