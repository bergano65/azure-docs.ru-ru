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
ms.date: 09/09/2020
ms.openlocfilehash: d135320d8dd9f86fbc313b17b8b55ed3c609e9dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595027"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Настройка самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR в фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается, как выполнять пакеты SQL Server Integration Services (SSIS) на Azure-SSIS Integration Runtime (Azure-SSIS IR) в фабрике данных Azure с помощью локальной среды выполнения интеграции (с локальным размещением), настроенной в качестве прокси-сервера. 

С помощью этой функции можно получить доступ к данным в локальной среде, не прибегая к [присоединению Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Эта функция полезна в том случае, если в корпоративной сети слишком сложная конфигурация или политика слишком ограничена, чтобы вы вставили Azure-SSIS IR в нее.

Эта функция разделяет задачу потока данных служб SSIS на две промежуточные задачи, когда это применимо: 
* **Локальная промежуточная задача**. Эта задача запускает компонент потока данных, который подключается к локальному хранилищу данных на собственном IR-сервере. Она перемещает данные из локального хранилища данных в промежуточную область в хранилище BLOB-объектов Azure или наоборот.
* **Промежуточная задача облачного**развертывания. Эта задача запускает компонент потока данных, который не подключается к локальному хранилищу данных на Azure-SSIS IR. Он перемещает данные из промежуточной области в хранилище BLOB-объектов Azure в облачное хранилище данных или наоборот.

Если задача потока данных перемещает данные из локальной среды в облако, то первая и вторая промежуточные задачи будут выполняться в локальных и облачных промежуточных задачах соответственно. Если задача потока данных перемещает данные из облака в локальную среду, то первая и вторая промежуточные задачи будут выполнять промежуточные и локальные задачи, соответственно. Если задача потока данных перемещает данные из локальной среды в локальную среду, то первая и вторая промежуточные задачи будут как локальные задачи промежуточного хранения. Если задача потока данных перемещает данные из облака в облако, эта функция неприменима.

Другие преимущества и возможности этой функции позволяют, например, настроить локальную IR в регионах, которые еще не поддерживаются Azure-SSIS IR, и разрешить общедоступный статический IP-адрес локальной среды IR в брандмауэре источников данных.

## <a name="prepare-the-self-hosted-ir"></a>Подготовка локальной среды IR

Чтобы использовать эту функцию, сначала создайте фабрику данных и настройте в ней Azure-SSIS IR. Если вы еще не сделали этого, следуйте инструкциям в разделе [настройка Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Затем вы настраиваете собственные IR в той же фабрике данных, в которой настроена Azure-SSIS IR. Дополнительные сведения см. в разделе Создание локальной среды [IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Наконец, вы скачиваете и устанавливаете последнюю версию самостоятельно размещенного IR, а также дополнительные драйверы и среду выполнения на локальном компьютере или виртуальной машине Azure следующим образом:
- Скачайте и установите последнюю версию локальной среды [IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Если в пакетах используются соединители по связыванию и внедрению баз данных (OLEDB)/Open DB, скачайте и установите соответствующие драйверы на том же компьютере, где установлена локальная среда IR, если это еще не сделано.  

  Если вы используете более раннюю версию драйвера OLEDB для SQL Server (SQL Server Native Client [SQLNCLI]), [скачайте 64-разрядную версию](https://www.microsoft.com/download/details.aspx?id=50402).  

  Если вы используете последнюю версию драйвера OLEDB для SQL Server (МСОЛЕДБСКЛ), [скачайте 64-разрядную версию](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  При использовании драйверов OLEDB/ODBC для других систем баз данных, таких как PostgreSQL, MySQL, Oracle и т. д., можно загрузить 64-разрядные версии с своих веб-сайтов.
- Если вы еще не сделали этого, [скачайте и установите 64-разрядную версию среды выполнения Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) на том же компьютере, где установлена локальная среда IR.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения

Если вы еще не сделали этого, создайте связанную службу хранилища BLOB-объектов Azure в той же фабрике данных, в которой настроена Azure-SSIS IR. Сведения о том, как это сделать, см. в статье [Создание связанной службы фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Обязательно выполните следующие действия.
- В качестве **хранилища данных**выберите **хранилище BLOB-объектов Azure**.  
- Для **подключения через среду выполнения интеграции**выберите **ауторесолвеинтегратионрунтиме** (не Azure-SSIS IR или локальное IR), так как мы используем Azure IR по умолчанию для получения учетных данных доступа к хранилищу BLOB-объектов Azure.
- В качестве **метода проверки подлинности**выберите **ключ учетной записи**, **URI SAS**или **субъект-служба**.  

    >[!TIP]
    >Если выбран метод **субъекта-службы** , предоставьте субъекту-службе по крайней мере роль *участника данных большого двоичного объекта хранилища*   . Дополнительные сведения см. в статье о [соединителе хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties).

![Подготовка связанной службы хранилища BLOB-объектов Azure для промежуточного хранения](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Настройка Azure-SSIS IR с локальным IR в качестве прокси-сервера

После подготовки локальной службы IR и связанной с хранилищем BLOB-объектов Azure для промежуточного хранения можно настроить новые или существующие Azure-SSIS IR с помощью локальной среды IR в качестве прокси-сервера на портале или приложении фабрики данных. Тем не менее, если существующий Azure-SSIS IR уже запущен, закройте его, а затем перезапустите.

1. В области **Настройка среды выполнения интеграции** перейдите к разделу **Общие параметры** и **параметры SQL** , нажав кнопку **Далее**. 

1. В разделе **Дополнительные параметры** выполните следующие действия.

   1. Установите флажок **настроить Self-Hosted Integration Runtime в качестве прокси-сервера для Azure-SSIS Integration Runtime** . 

   1. В раскрывающемся списке локально **размещенные Integration Runtime** выберите имеющуюся локальную среду IR в качестве прокси-сервера для Azure-SSIS IR.

   1. В раскрывающемся списке **связанная служба промежуточного хранилища** выберите существующую связанную службу хранилища BLOB-объектов Azure или создайте новую для промежуточного хранения.

   1. В поле **промежуточный путь** укажите контейнер больших двоичных объектов в выбранной учетной записи хранилища BLOB-объектов Azure или оставьте его пустым, чтобы использовать по умолчанию для промежуточного хранения.

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

Используя последнюю версию SSDT в качестве расширения проектов служб SSIS для Visual Studio или автономного установщика, можно найти новое `ConnectByProxy` свойство, добавленное в диспетчере соединений для поддерживаемых компонентов потока данных.
* [Скачивание расширения проектов служб SSIS для Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Скачать автономный установщик](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

При проектировании новых пакетов, содержащих задачи потока данных, с компонентами, которые обращаются к локальным данным, можно включить это свойство, задав для него *значение true* на панели **свойства** соответствующих диспетчеров соединений.

![Включить свойство Коннектбипрокси](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Это свойство также можно включить при запуске существующих пакетов без необходимости вручную менять их по одному.  Существует два варианта.
- **Вариант а**. Открытие, перестроение и повторное развертывание проекта, содержащего эти пакеты, с последним SSDT для запуска на Azure-SSIS IR. Затем можно включить свойство, задав для него *значение true* для соответствующих диспетчеров соединений. При запуске пакетов из среды SSMS эти диспетчеры соединений отображаются на вкладке **Диспетчеры соединений** всплывающего окна **выполнение пакета** .

  ![Включить Коннектбипрокси свойство2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Кроме того, можно включить свойство, задав для него *значение true* для соответствующих диспетчеров соединений, которые отображаются на вкладке **Диспетчеры соединений** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах фабрики данных.
  
  ![Включить Коннектбипрокси property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Вариант б.** Повторно разверните проект, содержащий эти пакеты, для выполнения в среде IR служб SSIS. Затем можно включить свойство, указав путь его свойства, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` и задав для него *значение true* в качестве переопределения свойства на вкладке **Дополнительно** во всплывающем окне **выполнение пакета** при выполнении пакетов из среды SSMS.

  ![Включить Коннектбипрокси property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Можно также включить свойство, указав его путь к свойству, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` и задав для него *значение true* в качестве переопределения свойства на вкладке **переопределения свойств** для [действия выполнение пакета служб SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при выполнении пакетов в конвейерах фабрики данных.
  
  ![Включить Коннектбипрокси property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Отладка локальных и облачных задач промежуточного хранения

В локальной среде IR журналы среды выполнения можно найти в папке *к:\програмдата\ссистелеметри* и в журналах выполнения локальных задач промежуточного хранения в папке *к:\програмдата\ссистелеметри\ексекутионлог* .  Журналы выполнения промежуточных задач в облаке можно найти в SSISDB или в указанных путях ведения журналов в зависимости от того, хранятся ли пакеты в SSISDB или нет. Вы также можете найти уникальные идентификаторы локальных промежуточных задач в журналах выполнения промежуточных задач облачного развертывания. 

![Уникальный идентификатор первой промежуточной задачи](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-on-premises-staging-tasks"></a>Использование проверки подлинности Windows в локальных промежуточных задачах

Если для локальных промежуточных задач требуется проверка подлинности Windows, [Настройте пакеты служб SSIS на использование одной и той же проверки подлинности Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Локальные задачи промежуточного хранения будут вызываться с помощью учетной записи локальной службы IR (по умолчанию*NT сервице\диахостсервице*), и ваши хранилища данных будут доступны с учетной записью проверки подлинности Windows. Для обеих учетных записей необходимо назначить определенные политики безопасности. На автономном IR-компьютере перейдите в раздел **Локальная политика безопасности**  >  **локальные**политики  >  **Назначение прав пользователя**и выполните следующие действия.

1. Назначьте *для процесса квоты на настройку памяти* и *замените политики маркеров на уровне процесса* на локальную учетную запись службы IR. Это должно происходить автоматически при установке автономного IR с учетной записью службы по умолчанию. Если это не так, назначьте эти политики вручную. Если вы используете другую учетную запись службы, назначьте ей те же политики.

1. Назначьте учетной записи проверки подлинности Windows *Вход в качестве политики службы* .

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Выставление счетов за локальные и облачные задачи промежуточного хранения

Счета за локальные промежуточные задачи, выполняемые на локальных IR-серверах, выставляются отдельно, так же как и любые действия перемещения данных, выполняемые на автономных IR-серверах. Это указано в статье [цены на конвейер данных фабрики данных Azure](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Промежуточные задачи облака, которые выполняются на Azure-SSIS IR, не выставляются отдельно, но счет за использование Azure-SSIS IR оплачивается согласно указаниям в разделе о [ценах на Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enabling-tls-12"></a>Включение TLS 1.2

Если вам нужно использовать надежный протокол шифрования (TLS 1,2) и отключить старые версии SSL/TLS на локальном компьютере, можно скачать и запустить сценарий *Main. cmd* , который можно найти в папке *кустомсетупскрипт/усерсценариос/TLS 1,2* нашего общедоступного контейнера предварительной версии.  С помощью [Обозреватель службы хранилища Azure](https://storageexplorer.com/)вы можете подключиться к нашему общедоступному контейнеру предварительной версии, ВВЕДЯ следующий URI SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Текущие ограничения

- В настоящее время поддерживаются только задачи потока данных с источниками OLEDB, ODBC и неструктурированных файлов или назначение OLEDB.
- В настоящее время поддерживаются только связанные службы хранилища BLOB-объектов Azure, настроенные с помощью *ключа учетной записи*, *URI подписанного URL-кода*или проверки подлинности *субъекта службы* .
- *Параметермаппинг* в ИСТОЧНИКе OLEDB в настоящее время не поддерживается. В качестве обходного решения используйте *команду SQL из переменной* в качестве *AccessMode* и используйте *выражение* для вставки переменных или параметров в команду SQL. Иллюстрация см. в разделе пакет *параметермаппингсампле. dtsx* , который можно найти в папке *селфхостедирпрокси/ограничения* нашего общедоступного контейнера предварительной версии. С помощью Обозреватель службы хранилища Azure вы можете подключиться к нашему общедоступному контейнеру предварительной версии, введя указанный выше URI SAS.

## <a name="next-steps"></a>Дальнейшие шаги

После настройки самостоятельно размещенного IR в качестве прокси-сервера для Azure-SSIS IR можно развернуть и запустить пакеты для доступа к данным в локальной среде как выполнение действий пакета служб SSIS в конвейерах фабрики данных. Дополнительные сведения см. в разделе [Запуск пакетов служб SSIS как выполнение действий пакета SSIS в конвейерах фабрики данных](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
