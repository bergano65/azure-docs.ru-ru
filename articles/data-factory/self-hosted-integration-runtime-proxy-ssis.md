---
title: Настройка локальной среды выполнения интеграции в качестве прокси для служб SSIS
description: Узнайте, как настроить автономную Integration Runtime в качестве прокси для Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 12/23/2019
ms.openlocfilehash: 48d4df5684c84e195810439912dd610f5af364d4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964487"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Настройка самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR в ADF

В этой статье описывается, как запускать пакеты SQL Server Integration Services (SSIS) в Azure-SSIS Integration Runtime (IR) в фабрике данных Azure (ADF) с локальным IR-сервером, настроенным в качестве прокси.  Эта функция позволяет получать доступ к данным в локальной среде, не [присоединяя Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Это полезно, если в корпоративной сети имеется чрезмерно сложная политика настройки или политики, позволяющая внедрять в нее Azure-SSIS IR.

Этот компонент разделит пакет, содержащий задачу потока данных с локальным источником данных, на две промежуточные задачи. первый, работающий на локальной среде IR, сначала переместит данные из локального источника данных в промежуточную область в хранилище BLOB-объектов Azure. второй запуск на Azure-SSIS IR переместит данные из промежуточной области в целевое назначение данных.

Эта функция также предоставляет другие преимущества и возможности, так как позволяет подготавливать собственные IR-данные в регионах, которые еще не поддерживаются Azure-SSIS IR, разрешать общедоступный статический IP адрес локальной среды IR в брандмауэре источников данных и т. д.

## <a name="prepare-self-hosted-ir"></a>Подготовка автономных IR

Чтобы использовать эту функцию, сначала необходимо создать файл ADF и подготовить Azure-SSIS IR под ним, если вы еще не сделали этого, выполнив [инструкции по подготовке Azure-SSIS IRной](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) статьи.

Затем необходимо подготовить собственный IR-файл в том же ADF-файле, где Azure-SSIS IR подготовлено, следуя инструкциям в статье Создание локальной [ИК-связи](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Наконец, необходимо скачать и установить последнюю версию локальной среды IR, а также дополнительные драйверы и среду выполнения на локальном компьютере или виртуальной машине Azure, как показано ниже.
- Скачайте и установите последнюю версию самостоятельно размещенного IR [отсюда.](https://www.microsoft.com/download/details.aspx?id=39717)
- Если вы используете соединители OLEDB в пакетах, скачайте и установите соответствующие драйверы OLEDB на том же компьютере, где установлена локальная среда IR, если это еще не сделано.  Если вы используете более раннюю версию драйвера OLEDB для SQL Server (SQLNCLI), можно загрузить 64-разрядную версию [отсюда](https://www.microsoft.com/download/details.aspx?id=50402).  Если вы используете последнюю версию драйвера OLEDB для SQL Server (MSOLEDBSQL), вы можете скачать 64-разрядную версию [отсюда](https://www.microsoft.com/download/details.aspx?id=56730).  Если вы используете драйверы OLEDB для других систем баз данных, таких как PostgreSQL, MySQL, Oracle и т. д., можно загрузить 64-разрядную версию с соответствующих веб-сайтов.
- Скачайте и установите среду C++ выполнения Visual (VC) на том же компьютере, где установлена автономная среда IR, если вы еще не сделали этого.  Загрузить 64-разрядную версию можно [здесь](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения

Создайте связанную службу хранилища больших двоичных объектов Azure в том же ADF-файле, где подготовлена ваша Azure-SSIS IR, если вы еще этого не сделали, следуя инструкциям в статье [Создание связанной службы ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Проверьте следующее:
- Выбрано **хранилище BLOB-объектов Azure** для **хранилища данных**
- **Ауторесолвеинтегратионрунтиме** выбран для **подключения через среду выполнения интеграции**
- Для **метода проверки подлинности** выбран **ключ учетной записи**/**URI SAS**/**субъекта-службы** .

>[!TIP]
>Если выбран **субъект-служба** , предоставьте по крайней мере **роль участника данных BLOB-объекта хранилища**. Дополнительные сведения см. в статье о [соединителе хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties).

![Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Настройка Azure-SSIS IR с локальным IR в качестве прокси-сервера

Теперь, когда вы подготовили собственную среду IR и связанную службу хранилища BLOB-объектов Azure для промежуточного хранения, вы можете настроить новую или существующую Azure-SSIS IR с помощью локальной среды IR в качестве прокси на портале или приложении ADF.  Если существующий Azure-SSIS IR работает, перед тем, как сделать это, завершите его, а затем перезапустите.

1. На панели настройки среды выполнения интеграции перейдите к разделу **Общие параметры** и **параметры SQL** , нажав кнопку **Далее** . 

1. В разделе **Дополнительные параметры** :

   1. Установите флажок **настроить автономную Integration Runtime как прокси для Azure-SSIS Integration Runtime** . 

   1. Для **автономных Integration Runtime**выберите имеющуюся локальную среду IR в качестве прокси-сервера для Azure-SSIS IR.

   1. Для **связанной службы промежуточного хранилища**выберите существующую связанную службу хранилища BLOB-объектов Azure или создайте новую для промежуточного хранения.

   1. В поле **промежуточный путь**укажите контейнер больших двоичных объектов в выбранной учетной записи хранилища BLOB-объектов Azure или оставьте пустым, чтобы использовать по умолчанию для промежуточного хранения.

   1. Выберите **Continue** (Продолжить).

   ![Дополнительные параметры с локальным IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Вы также можете настроить новый или существующий Azure-SSIS IR с локальным IR в качестве прокси-сервера с помощью PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Включение пакетов служб SSIS для подключения с помощью прокси-сервера

Используя последнюю версию SSDT с расширениями проектов SSIS для Visual Studio, которую можно скачать [отсюда](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) или как автономный установщик, который можно скачать [отсюда](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), можно найти новое свойство **ConnectByProxy**, добавленное в OLEDB/ Диспетчеры соединений с неструктурированными файлами.  

При проектировании новых пакетов, содержащих задачи потока данных с источниками OLEDB и плоских файлов для доступа к базам данных и файлам в локальной среде, это свойство можно включить, задав для него **значение true** на панели свойства соответствующих диспетчеров соединений.

![Включить свойство Коннектбипрокси](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Это свойство также можно включить при запуске существующих пакетов без необходимости вручную изменять их.  Имеются две возможности:
- Открытие, перестроение и повторное развертывание проекта, содержащего эти пакеты с последним SSDT, для запуска на Azure-SSIS IR. свойство можно включить, задав для него **значение true** для соответствующих диспетчеров соединений, которые отображаются на вкладке **Диспетчеры соединений** всплывающего окна "выполнение пакета" при запуске пакетов из среды SSMS.

  ![Включить Коннектбипрокси свойство2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Кроме того, свойство можно включить, задав для него **значение true** для соответствующих диспетчеров соединений, которые отображаются на вкладке **Диспетчеры соединений** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах ADF.
  
  ![Включить Коннектбипрокси property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Повторное развертывание проекта, содержащего эти пакеты для запуска служб SSIS IR. свойство можно включить, указав путь его свойства, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`и задав для него **значение true** в качестве переопределения свойства на вкладке **Дополнительно** во всплывающем окне Выполнение пакета при запуске пакетов из среды SSMS.

  ![Включить Коннектбипрокси property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Свойство также можно включить, указав путь его свойства, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`и задав для него **значение true** в качестве переопределения свойства на вкладке **переопределения свойств** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах ADF.
  
  ![Включить Коннектбипрокси property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Отладка первой и второй промежуточных задач

В локальной среде IR можно найти журналы среды выполнения в `C:\ProgramData\SSISTelemetry` папке и журналы выполнения первых промежуточных задач в `C:\ProgramData\SSISTelemetry\ExecutionLog` папке.  Журналы выполнения второй промежуточной задачи можно найти в SSISDB или в указанных путях ведения журналов в зависимости от того, хранятся ли пакеты в SSISDB или файловой системе, файловых ресурсах или файлах Azure соответственно.  Уникальные идентификаторы для первых промежуточных задач также можно найти в журналах выполнения второй промежуточной задачи, например 

![Уникальный идентификатор первой промежуточной задачи](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Использование проверки подлинности Windows в промежуточных задачах

Если для промежуточных задач в локальной среде IR требуется проверка подлинности Windows, необходимо [настроить пакеты служб SSIS для использования одной проверки подлинности Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Промежуточные задачи будут вызываться с помощью учетной записи локальной службы IR (`NT SERVICE\DIAHostService` по умолчанию), и доступ к хранилищам данных будет осуществляться с помощью учетной записи проверки подлинности Windows. Для обеих учетных записей необходимо назначить определенные политики безопасности. Следовательно, на самостоятельно размещенном IR-компьютере откройте `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` и выполните следующие действия.
- Назначьте **для процесса квоты на настройку памяти** и **замените политики маркеров на уровне процесса** на локальную учетную запись службы IR. Это необходимо сделать автоматически при установке автономного IR с учетной записью службы по умолчанию. Если вы используете другую учетную запись службы, назначьте ей те же политики.
- Назначьте учетной записи проверки подлинности Windows **Вход в качестве политики службы** .

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Выставление счетов за первую и вторую промежуточные задачи

Плата за выполнение первых промежуточных задач, выполняемых на локальном компьютере, будет взиматься отдельно так же, как и любые действия по перемещению данных, выполняемые на автономной IR, выставляются в соответствии с ценами, указанными в статье о [ценах на конвейер данных ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

За выполнение второй промежуточной задачи, выполняемой на Azure-SSIS IR, не будет выставлен счет по отдельности, но счет за использование Azure-SSIS IR будет взиматься согласно указаниям в статье о [ценах на Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Текущие ограничения

- В настоящее время поддерживаются только задачи потока данных с диспетчерами соединений ODBC, OLEDB и неструктурированных файлов, а также источники ODBC/OLEDB/неструктурированных файлов. 
- В настоящее время поддерживаются только связанные службы хранилища BLOB-объектов Azure с **ключом учетной записи**/**URI SAS**/проверки подлинности **субъекта-службы** .

## <a name="next-steps"></a>Дальнейшие действия

После настройки самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR можно развернуть и запустить пакеты для доступа к данным в локальной среде как выполнение действий пакета служб SSIS в конвейерах ADF. см. раздел [Запуск пакетов служб SSIS как выполнение действий пакета SSIS в конвейерах ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).