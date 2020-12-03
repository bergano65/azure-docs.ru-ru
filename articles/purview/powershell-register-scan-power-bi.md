---
title: Использование PowerShell для регистрации и проверки Power BI (Предварительная версия)
description: Узнайте, как использовать PowerShell для регистрации и сканирования Power BI клиента в Azure зрения.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553505"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Регистрация и проверка Power BI в Azure зрения (Предварительная версия) с помощью PowerShell 

В этой статье показано, как с помощью PowerShell настроить проверку клиента Power BI в каталоге Azure зрения.

## <a name="power-bi-authentication-background"></a>Фон проверки подлинности Power BI

Чтобы проверить артефакты в клиенте Power BI, каталог зрения должен подключиться к API-интерфейсу администратора Power BI. В настоящее время API администратора Power BI поддерживает два типа проверки подлинности:

- Управляемое удостоверение (MSI).
- Делегирование проверки подлинности пользователя.

> [!Note]
> Рекомендуется использовать MSI, если не требуется делегированная проверка подлинности пользователя.

## <a name="create-a-security-group"></a>Создание группы безопасности

Каждый каталог зрения имеет собственное управляемое удостоверение, назначенное системой, которому необходимо предоставить доступ к Power BI клиенту для включения проверки. Имя каталога можно использовать для поиска его удостоверения на портал Azure.

1. В [портал Azure](https://portal.azure.com)найдите Azure Active Directory.
1. Создайте новую группу безопасности в Azure Active Directory, выполнив инструкции по [созданию базовой группы и добавив члены с помощью Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Вы можете пропустить этот шаг, если у вас уже есть группа безопасности для использования.

1. Не забудьте выбрать безопасность в качестве **типа группы**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Тип группы безопасности":::

1. Добавьте управляемое удостоверение каталога в эту группу безопасности, выбрав члены, а затем — **Добавить участников**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Добавление управляемого экземпляра каталога в группу":::

1. Найдите свой каталог и выберите его.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Добавьте каталог, выполнив поиск":::

1. Вы должны увидеть уведомление об успешном выполнении, показывающее, что оно было добавлено.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Добавление MSI каталога успешно завершено":::

## <a name="associate-the-security-group-with-power-bi"></a>Связывание группы безопасности с Power BI

1. Войдите на [портал администрирования Power BI](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Добавить этот флаг функции:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Этот флаг включает функцию, которая позволяет связать группу безопасности. Например,

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Для просмотра страницы параметры клиента необходимо быть администратором Power BI.

1. Выберите **Параметры разработчика**  >  **Разрешить субъектам-службам использовать Power BI интерфейсы API только для чтения (Предварительная версия)**.
1. Выберите **определенные группы безопасности**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Изображение, показывающее, как разрешить субъектам-службам получать разрешения API администратора Power BI только для чтения ":::

    > [!Caution]
    > Если вы разрешаете созданной группе безопасности (которая имеет управляемое удостоверение каталога данных в качестве члена) использовать API-интерфейсы администрирования Power BI только для чтения, вы также можете получить доступ к метаданным (например, к панелям мониторинга и именам отчетов, владельцам, описаниям и т. д.) для всех артефактов Power BI в этом клиенте. После получения метаданных в Azure зрения, разрешения зрения, а не Power BI разрешения, определите, кто может просматривать эти метаданные.

    > [!Note]
    > Вы можете удалить группу безопасности из параметров разработчика, но ранее извлеченные метаданные не будут удалены из учетной записи зрения. Его можно удалить отдельно.

## <a name="register-power-bi-and-set-up-a-scan"></a>Регистрация Power BI и Настройка проверки

Теперь, когда вы предоставили разрешения каталога для подключения к API администратора вашего клиента Power BI, необходимо настроить проверку в каталоге. Для этого необходимо настроить и запустить сценарий PowerShell.

1. Скачайте и извлеките командлеты PowerShell для ADC.
1. Настройте скрипт, указав значения для назначений в верхней части скрипта.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Вы должны быть участником или владельцем подписки, в которой выполняются команды.

1. Запустите проверку, выполнив следующий скрипт:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Регистрация и сканирование Power BI

Рекомендуемым методом проверки подлинности является MSI. Однако для сканирования клиента Power BI, который находится в другом клиенте Azure, отличном от клиента каталога, используется делегированная проверка подлинности.

Чтобы выполнить делегированную проверку подлинности, необходимо иметь учетные данные администратора, а также Power BI учетные данные администратора. Необходимо также создать приложение Azure и предоставить его Power BI разрешений "клиент. ReadAll".

1. Перейдите к [портал Azure](https://portal.azure.com) и выполните поиск **регистраций приложений**.

1. В **Регистрация приложений** выберите **+ Новая регистрация**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Изображение, показывающее, как создать новую регистрацию приложения Azure":::

1. Введите название приложения.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="Регистрация нового приложения":::

1. После создания приложения выберите **разрешения API**, а затем **+ Добавить разрешение**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Изображение, показывающее, как добавить разрешение в приложение":::

1. Выберите **Power BI службы** в **разрешениях API запроса**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Изображение, показывающее, как выбрать службу PBI":::

1. Выберите **делегированные разрешения** и **клиент. чтение. все**. Нажмите кнопку **Добавить разрешения**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Изображение, показывающее, как запросить разрешения API":::

1. Нажмите **Предоставить согласие администратора**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Изображение, показывающее, как предоставить согласие администратора":::

1. Скопируйте **идентификатор приложения (клиента)** и значения **идентификатора каталога (клиента)** .  Эти значения будут использоваться при настройке проверки.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Изображение, показывающее Копирование идентификаторов клиента и клиентов":::

1. Настройте проверку в PowerShell. Сценарий предложит ввести учетные данные. В подписке Azure, которую вы планируете использовать, должна быть по крайней мере роль участника и роль администратора источника данных зрения.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Запустите проверку.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы приступить к работе с Azure зрения, см. раздел [Краткое руководство. Создание учетной записи Azure зрения](create-catalog-portal.md).
