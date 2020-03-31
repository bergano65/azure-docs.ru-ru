---
title: Настройка автономного времени выполнения интеграции в качестве прокси для SSIS
description: Узнайте, как настроить автономное время выполнения интеграции в качестве прокси для runtime интеграции Azure-SSIS.
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
ms.date: 03/27/2020
ms.openlocfilehash: 9a1923057bc318869f491791520aacb4d0d17591
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346619"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Налажить самостоятельно ещёхательную ИК-улику в качестве прокси-сервера для ИК Azure-SSIS на фабрике данных Azure

В этой статье описывается, как запускать пакеты интеграции серверов S'L Server (SSIS) на runtime интеграции Azure-SSIS (Azure-SSIS IR) в Azure Data Factory с самоходном время выполнения интеграции (самоходном ИК), настроенным в качестве прокси. 

С помощью этой функции вы можете получить доступ к данным на территории без присоединения к [ИК Azure-SSIS к виртуальной сети.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) Эта функция полезна, когда корпоративная сеть имеет слишком сложную конфигурацию или слишком ограничительную политику для внедрять в нее ИК Azure-SSIS.

Эта функция разделяет любую задачу потока данных SSIS, которая имеет локтевый источник данных, на две постановки: 
* Первая задача, которая выполняется на самостоятельно размещенной ИК, сначала перемещает данные из исходного источника данных в промежуточную область в хранилище Azure Blob.
* Вторая задача, которая выполняется на ИК Azure-SSIS, затем перемещает данные из промежуточной области в пункт назначения данных.

Другие преимущества и возможности этой функции позволяют, например, настроить самостоятельно размещенную ИК в регионах, которые еще не поддерживаются ИК Azure-SSIS, и разрешить общедоступный статический IP-адрес вашей самостоятельно размещенной ИК на брандмауэре ваших источников данных.

## <a name="prepare-the-self-hosted-ir"></a>Подготовка самостоятельного ИК

Чтобы использовать эту функцию, сначала создайте фабрику данных и навяжете в ней ИК Azure-SSIS. Если вы еще не сделали этого, следуйте инструкциям в [Setup-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Затем вы настраиваете свой самоуправляемый ИК на той же фабрике данных, где настроен ваш ИК Azure-SSIS. Для этого [см. Создать самостоятельно ежеразие ИК](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Наконец, вы загружаете и устанавливаете последнюю версию самостоятельно гостового ИК, а также дополнительные драйверы и время выполнения, на вашем предприимчивом компьютере или виртуальной машине Azure (VM), следующим образом:
- Скачать и установить последнюю версию [самостоятельного ИК](https://www.microsoft.com/download/details.aspx?id=39717).
- Если вы используете разъемы базы данных object Linking and Embedding Database (OLEDB) в пакетах, загрузите и установите соответствующие драйверы OLEDB на той же машине, где установлен ваш самоуправляемый ИК, если вы еще этого не сделали.  

  Если вы используете более раннюю версию драйвера OLEDB для сервера S'L Server (S'L Server Native Client, [то загрузите 64-битную версию.](https://www.microsoft.com/download/details.aspx?id=50402)  

  Если вы используете последнюю версию драйвера OLEDB для сервера S'L (MSOLEDBS'L), [загрузите 64-битную версию.](https://www.microsoft.com/download/details.aspx?id=56730)  
  
  Если вы используете драйверы OLEDB для других систем баз данных, таких как PostgreS'L, MyS'L, Oracle и так далее, вы можете скачать 64-разрядные версии с их веб-сайтов.
- Если вы еще не сделали этого, [загрузите и установите 64-битную версию Visual C '(VC) время выполнения](https://www.microsoft.com/download/details.aspx?id=40784) на той же машине, где установлен ваш самоуправляемый ИК.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Подготовьте службу хранения, связанную с Azure Blob, для постановки

Если вы еще этого не сделали, создайте службу хранения, связанную с Azure Blob, на той же фабрике данных, где настроена иСБ Azure-SSIS. Для этого см. [Создать сервис, связанный с фабрикой данных Azure, подобие данных.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) Обязательно сделайте следующее:
- Для **хранилища данных**выберите **хранилище Azure Blob.**  
- Для **подключения через время выполнения интеграции**выберите **AutoResolveIntegrationRuntime** (не ваш ИК Azure-SSIS, ни ваш самостоятельно размещенный ИК), потому что мы используем ИК по умолчанию Azure для получения учетных данных доступа для вашего хранилища Azure Blob  
- Для **метода аутентификации**выберите **ключ учетной записи,** **SAS URI**или директор **службы.**  

    >[!TIP]
    >Если вы выберете **основной** метод службы, предоставьте директору службы хотя бы роль  *вкладчика хранилища данных Blob.* Для получения дополнительной информации обратитесь к [разъему для хранения данных Azure Blob.](connector-azure-blob-storage.md#linked-service-properties)

![Подготовьте службу хранения, связанную с Azure Blob, для постановки](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Налажить ИК Azure-SSIS с помощью самостоятельно гостоминиумного ИК в качестве прокси-сервера

Подготовив для постановки сервис для хранения данных, связанный с самоуправляемым ИК и Azure Blob, теперь можно настроить новый или существующий ИК Azure-SSIS с самостоятельно йохатом ИК в качестве прокси-сервера на портале или приложении для фабрики данных. Однако, прежде чем сделать это, если ваш существующий ИК Azure-SSIS уже работает, остановите его, а затем перезапустите.

1. В панели **настройки времени выполнения интеграции** пройдите мимо **разделов общих настроек** и **настроек S'L,** выбрав **следующий.** 

1. В разделе **Расширенные настройки** сделайте следующее:

   1. Выберите **Set up Self-Hosted Интеграция Runtime в качестве прокси для вашего Azure-SSIS Интеграция Runtime** флажок. 

   1. В списке **самохотводчатой интеграции Runtime** выберите существующую иС-усевую в качестве прокси для ИК Azure-SSIS.

   1. В списке **перепадов, связанных с системой хранения,** выберите существующую службу хранения, связанную с Azure Blob, или создайте новую службу для постановки.

   1. В поле **траектории постановки** укажите контейнер blob в выбранной учетной записи хранения Azure Blob или оставьте его пустым для использования по умолчанию для постановки.

   1. Выберите **Продолжить**.

   ![Расширенные настройки с самостоятельной ИК](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Вы также можете настроить свой новый или существующий ИК Azure-SSIS с самостоятельной ИК в качестве прокси с помощью PowerShell.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Включить пакеты SSIS для подключения по доверенности

Используя либо новейший SSDT с расширением SSIS Projects для Visual Studio, `ConnectByProxy` либо автономным установщиком, вы можете найти новое свойство, которое было добавлено в OLEDB или менеджеры соединения flat File.
* [Скачать SSDT с расширением SSIS Проекты для Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Скачать автономный установщик](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

При разработке новых пакетов, содержащих задачи потока данных с помощью OLEDB или flat File, которые позволяют получить доступ к базам данных или файлам на местах, вы можете включить это свойство, установив его в *панели* **Свойств** соответствующих менеджеров соединения.

![Включить свойство ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Вы также можете включить это свойство при запуске существующих пакетов, без необходимости вручную менять их по одному.  Имеются две возможности:
- **Вариант A**: Откройте, перестроить и передислоцировать проект, содержащий эти пакеты с последним SSDT для выполнения на икс-иК Azure-SSIS. Затем можно включить свойство, установив его на *True* для соответствующих менеджеров соединения. При запуске пакетов из SSMS эти менеджеры подключения отображаются во вкладке **«Менеджеры соединения»** всплывающем окне **пакета выполнения.**

  ![Включить свойство ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Вы также можете включить свойство, установив его на *Верно* для соответствующих менеджеров соединения, которые отображаются на вкладке **менеджеров соединения** [действия выполнения SSIS пакет,](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) когда они работают пакеты в конвейерах Data Factory.
  
  ![Включить свойство ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Вариант B:** Передислоцируйте проект, содержащий эти пакеты, для запуска на SSIS IR. Затем можно включить свойство, предоставив его свойство `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`путь, и установив его на *True* как свойство переопределить на **расширенной** вкладке окна пакета **выполнения,** когда вы работаете пакеты из SSMS.

  ![Включить свойство ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Вы также можете включить свойство, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`предоставив его путь свойства, и установив его на *True* как переопределение свойства на вкладке **Property Overrides** [действия пакета Выполнения SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) при запуске пакетов в конвейерах Data Factory.
  
  ![Включить свойство ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Отделочные задачи первой и второй постановки

На вашем самостоятельно мозяхнувом ИК-журнале можно найти журналы времени выполнения в папке *C: «ProgramData»SSISTelemetry* и журналы выполнения первых постановочных задач в папке *C: «ProgramData»SSISTelemetry-ExecutionLog.*  Вы можете найти журналы выполнения вторых промежуточных задач в SSISDB или указанных траекториях регистрации, в зависимости от того, храните ли вы пакеты в SSISDB или файловой системе, файлах или файлах Azure. Вы также можете найти уникальные иди первых постановочных задач в журналах выполнения задач второй постановки. 

![Уникальный идентификатор первого постановочного задания](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Использование аутентификации Windows в постановке задач

Если постановочные задачи на независимом ит-ней требуют проверки подлинности Windows, [настроьте пакеты SSIS для использования той же проверки подлинности Windows.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15) 

Ваши постановочные задачи будут использоваться с помощью учетной записи самостоятельной иР-службы *(NT SERVICE-DIAHostService*, по умолчанию), а ваши хранилища данных будут доступны с учетной записью проверки подлинности Windows. Обе учетные записи требуют приписаниваемых определенным ими политикам безопасности. На самохпарнюшерской ИК-машине перейдите на**локальное назначение****локальных** > политик **политик** > и прав пользователей, а затем сделайте следующее:

1. Назначайте *квоты на настройку памяти для процесса* и *заменяйте* политики маркеров уровня процесса на учетную запись иС-услуги с самостоятельной размещенной учетной записью. Это должно происходить автоматически при установке самостоятельного ИК-аккаунта по умолчанию. Если это не так, назначайте эти политики вручную. Если вы используете другую учетную запись службы, назначаем ей одни и те же политики.

1. Назначить журнал в *качестве политики службы* учетной записи аутентификации Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Выставление счетов за первую и вторую постановки задач

Первые постановочные задачи, которые выполняются на вашем самостоятельном ИК-счете, выставляются отдельно, так же, как и любые действия по движению данных, которые выполняются на самостоятельно йохастом ИК- Это указано в статье о [ценах на конвейер данных Azure Data Factory.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

Вторые постановочные задачи, которые выполняются на ИК Azure-SSIS, не выставляются отдельно, но ваш запущенный ИК Azure-SSIS выставлен счет, как указано в статье [о ценах Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Включение TLS 1.2

Если вам нужно использовать сильный криптографии / более безопасный сетевой протокол (TLS 1.2) и отключить старые версии SSL/TLS на вашем самостоятельном размещении ИК, вы можете скачать и запустить *сценарий main.cmd,* который можно найти в *CustomSetupScript/UserScenarios/TLS 1.2* папку нашего публичного контейнера предварительного просмотра.  Используя [Azure Storage Explorer,](https://storageexplorer.com/)вы можете подключиться к нашему общедоступному контейнеру предварительного просмотра, введя следующий SAS URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Текущие ограничения

- В настоящее время поддерживаются только задачи потока данных с источниками Open Database Connectivity (ODBC)/OLEDB/Flat File. 
- В настоящее время поддерживаются только службы хранения, связанные с Системой хранения Azure Blob, настроенные с *ключом учетной записи,* *URI —общая подпись доступа (SAS)* или проверка подлинности *основного сервиса.*
- *Параметрирование* в OLEDB Source пока не поддерживается. В качестве обходного пути, пожалуйста, используйте *команду S'L From Variable* в качестве *AccessMode* и используйте *Expression* для вставки переменных/параметров в команду S'L. В качестве иллюстрации см. пакет *ParameterMappingSample.dtsx,* который можно найти в папке *SelfHostedIRProxy/Limitations* нашего публичного контейнера предварительного просмотра. Используя Azure Storage Explorer, вы можете подключиться к нашему общедоступному контейнеру предварительного просмотра, введя вышеуказанный SAS URI.

## <a name="next-steps"></a>Дальнейшие действия

После настройки иТогового ик-сервера в качестве прокси для ИК Azure-SSIS можно развернуть и запустить пакеты для доступа к данным на месте, как выполнение действий пакета SSIS в конвейерах Data Factory. Чтобы узнать, как [выполнить пакеты SSIS как выполнение sSIS пакет деятельности в конвейерах Data Factory.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
