---
title: Аутентификация заданий Azure Stream Analytics для вывода данных в Azure Data Lake Storage 1-го поколения
description: В этой статье объясняется, как использовать управляемые удостоверения для аутентификации заданий Azure Stream Analytics для вывода данных в Azure Data Lake Storage 1-го поколения.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 43947413f061ec8b366392b676e848ebf5e6484e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570119"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Аутентификация заданий Azure Stream Analytics с помощью управляемых удостоверений для вывода данных в Azure Data Lake Storage 1-го поколения (предварительная версия)

Azure Stream Analytics поддерживает аутентификацию с помощью управляемого удостоверения для вывода данных в Azure Data Lake Storage (ADLS) 1-го поколения. Удостоверение — это зарегистрированное в Azure Active Directory управляемое приложение, представляющее данное задание Stream Analytics и используемое для аутентификации в целевом ресурсе. Управляемые удостоверения устраняют ограничения пользовательских методов аутентификации, такие как необходимость повторной аутентификации из-за изменения пароля или после истечения срока действия пользовательских токенов (каждые 90 дней). Кроме того, управляемые удостоверения позволяют автоматизировать развертывания заданий Stream Analytics, которые выводят данные в Azure Data Lake Storage 1-го поколения.

Ознакомьтесь с записью блога о [восьми новых возможностях Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/), чтобы зарегистрироваться для использования этой предварительной версии и получить подробные сведения о новых возможностях.

В этой статье показано три способа включения управляемого удостоверения для задания Azure Stream Analytics, которое выводит данные в Azure Data Lake Storage 1-го поколения: с помощью портала Azure (развертывания шаблона Azure Resource Manager) и средств Azure Stream Analytics для Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Портал Azure

1. Начните с создания задания Stream Analytics или открытия имеющегося задания на портале Azure. В строке меню, расположенной в левой части экрана, выберите **Managed Identity (preview)** (Управляемое удостоверение (предварительная версия)) в разделе **Настройка**.

   ![Настройка управляемого удостоверения Stream Analytics предварительной версии](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. В окне, которое отобразится справа, выберите **Use System-assigned Managed Identity (preview)** (Использовать назначаемое системой управляемое удостоверение (предварительная версия)). Нажмите кнопку **Сохранить** субъекту-службе для идентификации и задание Stream Analytics в Azure Active Directory. Жизненным циклом нового удостоверения будет управлять Azure. При удалении задания Stream Analytics Azure автоматически удаляет связанное удостоверение (то есть субъект-службу).

   Если конфигурация сохраняется, идентификатор объекта (OID) субъекта-службы отображается в качестве идентификатора субъекта, как показано ниже:

   ![Идентификатор субъекта-службы Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Субъект-служба имеет то же имя, что и задание Stream Analytics. Например, если имя задания — **MyASAJob**, имя созданного субъекта-службы будет также **MyASAJob**.

3. В окне свойств выходных данных в приемнике выходных данных ADLS 1-го поколения щелкните раскрывающийся список режима аутентификации, а затем выберите **Managed Identity (preview)** (Управляемое удостоверение (предварительная версия)).

4. Укажите остальные свойства. Дополнительные сведения о создании выходных данных для ADLS см. в статье [Потоковая передача данных из большого двоичного объекта службы хранилища Azure в Data Lake Storage 1-го поколения с помощью Azure Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Закончив, нажмите кнопку **Сохранить**.

   ![Настройка Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Перейдите к странице обзора ADLS 1-го поколения и щелкните **Обозреватель данных**.

   ![Обзор настройки Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. На панели обозревателя данных выберите **Доступ** и щелкните **Добавить** в области доступа.

   ![Настройка доступа к Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. В текстовом поле в области **Select user or group** (Выбор пользователя или группы) укажите имя субъекта-службы. Не забывайте, что имя субъекта-службы совпадает с именем соответствующего задания Stream Analytics. Как только вы начнете вводить имя субъекта, оно отобразится под текстовым полем. Выберите необходимое имя субъекта-службы, а затем щелкните **Выбрать**.

   ![Выбор имени субъекта-службы](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. В области **Разрешения** выберите разрешения **Запись** и **Выполнить** и установите переключатель **К этой папке и всем вложенным элементам**. Нажмите кнопку **ОК**.

   ![Выбор разрешений на запись и выполнение](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Субъект-служба находится в списке **Назначенные разрешения** в области **Доступ**, как показано ниже. Теперь вы можете вернуться назад и приступить к заданию Stream Analytics.

   ![Список доступа Stream Analytics на портале](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Дополнительные сведения о разрешениях файловой системы Azure Data Lake Storage 1-го поколения см. в статье [Контроль доступа в Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Средства Stream Analytics для Visual Studio

1. В JobConfig.json задайте **Использовать назначенное системой удостоверение** значение **True**.

   ![Управляемые идентификаторы конфигурации задания Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. В окне свойств выходных данных в приемнике выходных данных ADLS 1-го поколения щелкните раскрывающийся список режима аутентификации, а затем выберите **Managed Identity (preview)** (Управляемое удостоверение (предварительная версия)).

   ![Управляемые удостоверения выходных данных ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Укажите остальные свойства и щелкните **Сохранить**.

4. Щелкните **Отправить в Azure** в редакторе запросов.

   При отправке задания инструменты выполняют два действия.

   * Автоматически создают субъект-службу для идентификации задания Stream Analytics в Azure Active Directory. Жизненным циклом нового удостоверения будет управлять Azure. При удалении задания Stream Analytics Azure автоматически удаляет связанное удостоверение (то есть субъект-службу).

   * Автоматически установите разрешения на **запись** и **выполнение** для пути префикса ADLS 1-го поколения, используемого в задании, и назначьте его этой папке и ее всем дочерним элементам.

5. Вы можете создать шаблоны Resource Manager со следующим свойством, используя [пакет Stream Analytics CI.CD Nuget](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) версии 1.5.0 или более поздней на компьютере для сборки (вне Visual Studio). Следуйте инструкциям по развертыванию шаблона Resource Manager, приведенным в следующем разделе, чтобы получить субъект-службу и предоставить доступ субъект-службе через PowerShell.

## <a name="resource-manager-template-deployment"></a>Развертывание шаблонов Resource Manager

1. Вы можете создать ресурс *Microsoft.StreamAnalytics/streamingjobs* с использованием управляемого удостоверения, включив в раздел ресурсов шаблона Resource Manager следующее свойство:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Это свойство указывает Azure Resource Manager, что требуется создать удостоверение для задания Azure Stream Analytics и управлять им.

   **Пример задания**

    ```json
    {
      "Name": "AsaJobWithIdentity",
      "Type": "Microsoft.StreamAnalytics/streamingjobs",
      "Location": "West US",
      "Identity": {
        "Type": "SystemAssigned",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
        "outputs": [
          {
            "name": "string",
            "properties":{
              "datasource": {
                "type": "Microsoft.DataLake/Accounts",
                "properties": {
                  "accountName": "myDataLakeAccountName",
                  "filePathPrefix": "cluster1/logs/{date}/{time}",
                  "dateFormat": "YYYY/MM/DD",
                  "timeFormat": "HH",
                  "authenticationMode": "Msi"
                }
              }
   ```
  
   **Пример ответа задания**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
      }
   ```

   Запишите идентификатор субъекта (principalId) из ответа задания для предоставления доступа к необходимому ресурсу ADLS.

   **Идентификатор клиента** (tenantId) — это идентификатор клиента Azure Active Directory, где создан субъект-служба. Субъект-служба создается в клиенте Azure, который является доверенным для этой подписки.

   **Type** указывает тип управляемого удостоверения, как описано в типах управляемых удостоверений. Поддерживается только тип удостоверения, назначаемого системой.

2. Предоставьте доступ субъекту-службе с помощью PowerShell. Чтобы предоставить доступ субъекту-службе с помощью PowerShell, выполните следующую команду:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** — идентификатор объекта субъекта-службы. Он отображается на экране портала после создания субъекта-службы. В случае создания задания путем развертывания шаблона Resource Manager идентификатор объекта отображается в свойстве идентификаторов в ответе задания.

   **Пример**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Чтобы узнать больше о вышеуказанная команда PowerShell, обратитесь к [AzDataLakeStoreItemAclEntry набора](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) документации.

## <a name="limitations"></a>Ограничения
Эта функция не поддерживает следующее:

1.  **Доступ несколькими клиентами**: Субъекта-службы, созданные для данного задания Stream Analytics будет находиться в клиенте Azure Active Directory, на котором был создан задания и не может использоваться для ресурсов, который находится на другом клиенте Azure Active Directory. Таким образом MSI можно использовать только в ADLS Gen 1 ресурсы, которые находятся в том же клиенте Azure Active Directory задания Azure Stream Analytics. 

2.  **[Назначить удостоверение пользователя](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**: не поддерживается это означает, что пользователь не может перейти в свои собственные субъекта-службы для использования их заданием Stream Analytics. Для создания субъекта-службы Azure Stream Analytics. 


## <a name="next-steps"></a>Дальнейшие действия

* [Создание выходных данных Data Lake Store с помощью Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Локальное тестирование запросов Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md) 
