---
title: Настройка локальной среды выполнения интеграции в качестве прокси для служб SSIS
description: Узнайте, как настроить локальную среду выполнения интеграции в качестве учетной записи-посредника для Azure-SSIS Integration Runtime.
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
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187684"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Настройка самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR в фабрике данных Azure

В этой статье описывается, как выполнять пакеты SQL Server Integration Services (SSIS) на Azure-SSIS Integration Runtime (Azure-SSIS IR) в фабрике данных Azure с помощью локальной среды выполнения интеграции (с локальным размещением), настроенной в качестве прокси-сервера. 

С помощью этой функции можно получить доступ к данным в локальной среде, не прибегая к [присоединению Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Эта функция полезна в том случае, если в корпоративной сети слишком сложная конфигурация или политика слишком ограничена, чтобы вы вставили Azure-SSIS IR в нее.

Эта функция разделяет пакеты, содержащие задачу потока данных с локальным источником данных, на две промежуточные задачи: 
* Первая задача, которая выполняется на локальной среде IR, сначала перемещает данные из локального источника данных в промежуточную область в хранилище BLOB-объектов Azure.
* Вторая задача, которая выполняется на Azure-SSIS IR, затем перемещает данные из промежуточной области в целевое назначение данных.

Другие преимущества и возможности этой функции позволяют, например, настроить локальную IR в регионах, которые еще не поддерживаются Azure-SSIS IR, и разрешить общедоступный статический IP-адрес локальной среды IR в брандмауэре источников данных.

## <a name="prepare-the-self-hosted-ir"></a>Подготовка локальной среды IR

Чтобы использовать эту функцию, сначала создайте фабрику данных и настройте в ней Azure-SSIS IR. Если вы еще не сделали этого, следуйте инструкциям в разделе [настройка Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Затем вы настраиваете собственные IR в той же фабрике данных, в которой настроена Azure-SSIS IR. Дополнительные сведения см. в разделе Создание локальной среды [IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Наконец, вы скачиваете и устанавливаете последнюю версию самостоятельно размещенного IR, а также дополнительные драйверы и среду выполнения на локальном компьютере или виртуальной машине Azure следующим образом:
- Скачайте и установите последнюю версию локальной среды [IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Если в пакетах используются соединители по связыванию и внедрению баз данных (OLEDB), скачайте и установите соответствующие драйверы OLEDB на том же компьютере, где установлена локальная среда IR, если это еще не сделано.  

  Если вы используете более раннюю версию драйвера OLEDB для SQL Server (SQL Server Native Client [SQLNCLI]), [скачайте 64-разрядную версию](https://www.microsoft.com/download/details.aspx?id=50402).  

  Если вы используете последнюю версию драйвера OLEDB для SQL Server (МСОЛЕДБСКЛ), [скачайте 64-разрядную версию](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Если вы используете драйверы OLEDB для других систем баз данных, таких как PostgreSQL, MySQL, Oracle и т. д., можно загрузить 64-разрядные версии с своих веб-сайтов.
- Если вы еще не сделали этого, [скачайте и установите 64-разрядную версию среды выполнения C++ Visual (VC)](https://www.microsoft.com/download/details.aspx?id=40784) на том же компьютере, где УСТАНОВЛЕНА локальная среда IR.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения

Если вы еще не сделали этого, создайте связанную службу хранилища BLOB-объектов Azure в той же фабрике данных, в которой настроена Azure-SSIS IR. Сведения о том, как это сделать, см. в статье [Создание связанной службы фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Обязательно выполните следующие действия.
- В качестве **хранилища данных**выберите **хранилище BLOB-объектов Azure**.  
- Для **подключения через среду выполнения интеграции**выберите **ауторесолвеинтегратионрунтиме**.  
- В качестве **метода проверки подлинности**выберите **ключ учетной записи**, **URI SAS**или **субъект-служба**.  

    >[!TIP]
    >Если выбран **субъект-служба**, предоставьте по крайней мере роль *участника данных BLOB-объекта хранилища* роли. Дополнительные сведения см. в статье о [соединителе хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties).

![Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Настройка Azure-SSIS IR с локальным IR в качестве прокси-сервера

После подготовки локальной службы IR и связанной с хранилищем BLOB-объектов Azure для промежуточного хранения можно настроить новые или существующие Azure-SSIS IR с помощью локальной среды IR в качестве прокси-сервера на портале или приложении фабрики данных. Тем не менее, если существующий Azure-SSIS IR уже запущен, закройте его, а затем перезапустите.

1. В области **Настройка среды выполнения интеграции** перейдите к разделу **Общие параметры** и **параметры SQL** , нажав кнопку **Далее**. 

1. В разделе **Дополнительные параметры** выполните следующие действия.

   1. Установите флажок **настроить автономную Integration Runtime как прокси для Azure-SSIS Integration Runtime** . 

   1. В раскрывающемся списке локально **размещенные Integration Runtime** выберите имеющуюся локальную среду IR в качестве прокси-сервера для Azure-SSIS IR.

   1. В раскрывающемся списке **связанная служба промежуточного хранилища** выберите существующую связанную службу хранилища BLOB-объектов Azure или создайте новую для промежуточного хранения.

   1. В поле **промежуточный путь** укажите контейнер больших двоичных объектов в выбранной учетной записи хранилища BLOB-объектов Azure или оставьте его пустым, чтобы использовать по умолчанию для промежуточного хранения.

   1. Выберите **Продолжить**.

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

Используя последнюю версию SSDT с расширением проектов SSIS для Visual Studio или автономный установщик, можно найти новое свойство `ConnectByProxy`, добавленное в диспетчере соединений OLEDB или неструктурированных файлов.
* [Скачивание расширения SSDT с проектами служб SSIS для Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Скачать автономный установщик](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

При проектировании новых пакетов, содержащих задачи потока данных с источниками OLEDB или неструктурированными файлами, которые позволяют осуществлять доступ к базам данных или файлам в локальной среде, это свойство можно включить, задав для него *значение true* на панели **свойств** соответствующих диспетчеров соединений.

![Включить свойство Коннектбипрокси](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Это свойство также можно включить при запуске существующих пакетов без необходимости вручную менять их по одному.  Существует два варианта.
- **Вариант а**. Открытие, перестроение и повторное развертывание проекта, содержащего эти пакеты, с последним SSDT для запуска на Azure-SSIS IR. Затем можно включить свойство, задав для него *значение true* для соответствующих диспетчеров соединений. При запуске пакетов из среды SSMS эти диспетчеры соединений отображаются на вкладке **Диспетчеры соединений** всплывающего окна **выполнение пакета** .

  ![Включить Коннектбипрокси свойство2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Кроме того, можно включить свойство, задав для него *значение true* для соответствующих диспетчеров соединений, которые отображаются на вкладке **Диспетчеры соединений** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах фабрики данных.
  
  ![Включить Коннектбипрокси property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Вариант б.** Повторно разверните проект, содержащий эти пакеты, для выполнения в среде IR служб SSIS. Затем можно включить свойство, указав путь его свойства, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`и задав для него *значение true* в качестве переопределения свойства на вкладке **Дополнительно** во всплывающем окне **выполнение пакета** при выполнении пакетов из среды SSMS.

  ![Включить Коннектбипрокси property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Можно также включить свойство, указав путь к его свойству, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`и задав для него *значение true* в качестве переопределения свойства на вкладке **переопределения свойств** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах фабрики данных.
  
  ![Включить Коннектбипрокси property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Отладка первой и второй промежуточных задач

В локальной среде IR журналы среды выполнения можно найти в папке *к:\програмдата\ссистелеметри* и в журналах выполнения первых промежуточных задач в папке *к:\програмдата\ссистелеметри\ексекутионлог* .  Журналы выполнения второй промежуточной задачи можно найти в SSISDB или в указанных путях ведения журналов в зависимости от того, хранятся ли пакеты в SSISDB или файловой системе, файловых ресурсах или файлах Azure. Кроме того, можно найти уникальные идентификаторы первых промежуточных задач в журналах выполнения второй промежуточной задачи. 

![Уникальный идентификатор первой промежуточной задачи](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Использование проверки подлинности Windows в промежуточных задачах

Если промежуточные задачи на локальном IR-сервере нуждаются в проверке подлинности Windows, [Настройте пакеты служб SSIS для использования одной и той же проверки подлинности Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Промежуточные задачи будут вызываться с помощью учетной записи локальной службы IR (по умолчанию*NT сервице\диахостсервице*), и доступ к хранилищам данных будет осуществляться с помощью учетной записи проверки подлинности Windows. Для обеих учетных записей необходимо назначить определенные политики безопасности. На самостоятельно размещенном IR-компьютере перейдите в раздел **Локальная политика безопасности** > **Локальные политики** > **Назначение прав пользователя**и выполните следующие действия.

1. Назначьте *для процесса квоты на настройку памяти* и *замените политики маркеров на уровне процесса* на локальную учетную запись службы IR. Это должно происходить автоматически при установке автономного IR с учетной записью службы по умолчанию. Если вы используете другую учетную запись службы, назначьте ей те же политики.

1. Назначьте учетной записи проверки подлинности Windows *Вход в качестве политики службы* .

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Выставление счетов за первую и вторую промежуточные задачи

Счета за первые промежуточные задачи, выполняемые на собственном IR-сервере, выставляются отдельно, так же как и любые действия перемещения данных, выполняемые на автономных IR-серверах. Это указано в статье [цены на конвейер данных фабрики данных Azure](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Для второй промежуточной задачи, выполняемой в Azure-SSIS IR, не взимается оплата по отдельности, но счет за использование Azure-SSIS IR оплачивается согласно указаниям в статье о [ценах на Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Текущие ограничения

- В настоящее время поддерживаются только задачи потока данных с диспетчерами соединений ODBC, OLEDB или неструктурированных файлов, а также с источниками данных ODBC, OLEDB и неструктурированных файлов. 
- В настоящее время поддерживаются только связанные службы хранилища BLOB-объектов Azure, настроенные с помощью *ключа учетной записи*, *URI подписанного URL-кода*или проверки подлинности *субъекта службы* .

## <a name="next-steps"></a>Следующие шаги

После настройки самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR можно развернуть и запустить пакеты для доступа к данным в локальной среде как выполнение действий пакета служб SSIS в конвейерах фабрики данных. Дополнительные сведения см. в разделе [Запуск пакетов служб SSIS как выполнение действий пакета SSIS в конвейерах фабрики данных](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
