---
title: Использование управляемого удостоверения, назначаемого системой, на виртуальной машине Windows для доступа к службе хранилища Azure
description: Из этого руководства вы узнаете, как получить доступ к службе хранилища Azure с помощью управляемого удостоверения, назначаемого системой, на виртуальной машине Windows.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147ee2450a6a67f8ca02149105533401d038a53a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191083"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Руководство по Использование назначаемого системой управляемого удостоверения виртуальной машины Windows для доступа к службе хранилища Azure с помощью ключа доступа

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


> [!IMPORTANT] 
> Служба хранилища Azure теперь поддерживает аутентификацию Azure AD. Рекомендуется использовать [аутентификацию Azure AD](tutorial-vm-windows-access-storage.md) вместо ключей доступа. 


В этом руководстве описывается, как получить ключи доступа для учетной записи хранения с помощью управляемого удостоверения виртуальной машины Windows, назначаемого системой. Вы можете использовать ключи доступа к хранилищу стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK для службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью PowerShell службы хранилища Azure. Вы научитесь:


> [!div class="checklist"]
> * Создание учетной записи хранения
> * предоставлять виртуальной машине доступ к ключам учетной записи хранения в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения ключей доступа к хранилищу из Resource Manager. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Создание учетной записи хранения 

Если у вас еще нет учетной записи хранения, создайте ее. Этот шаг можно пропустить и предоставить виртуальной машине доступ на основе управляемого удостоверения, назначаемого системой, к ключам существующей учетной записи хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите имя учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Позже мы отправим и скачаем файл в новую учетную запись хранения. Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** слева в разделе "Служба BLOB-объектов".
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Предоставление назначаемому системой управляемому удостоверению виртуальной машины прав на использование ключей доступа для учетных записей хранения 

В службе хранилища Azure не встроена поддержка проверки подлинности Azure AD.  Но вы можете использовать управляемое удостоверение, назначаемое системой, на виртуальной машине, чтобы получить ключи доступа для учетных записей хранения из Resource Manager и последующего доступа к хранилищу.  На этом шаге мы предоставим назначаемому системой управляемому удостоверению виртуальной машины доступ к ключам учетной записи хранения.   

1. Вернитесь к только что созданной учетной записи хранения.  
2. Щелкните ссылку **Управление доступом (IAM)** на панели слева.  
3. Щелкните **+ Добавить назначение ролей** в верхней части страницы, чтобы добавить новое назначение роли для виртуальной машины.
4. Для параметра **Роль** в правой части страницы выберите значение "Роль службы оператора ключей учетной записи хранения". 
5. Рядом с ним в раскрывающемся списке **Назначение доступа к** выберите ресурс "Виртуальная машина".  
6. Убедитесь, что нужная подписка присутствует в раскрывающемся списке **Подписки**, и установите для параметра **Группы ресурсов** значение "Все группы ресурсов".  
7. И наконец, в разделе **Выбрать** выберите в раскрывающемся списке нужную виртуальную машину Windows и нажмите кнопку **Сохранить**. 

    ![Замещающий текст](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью управляемого удостоверения виртуальной машины, назначаемого системой, для вызова Azure Resource Manager 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали. 

На этом этапе вы примените командлеты PowerShell для Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview), прежде чем продолжить.

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и вверху в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе.
4. С помощью командлета PowerShell Invoke-WebRequest выполните запрос к локальной конечной точке управляемых удостоверений для ресурсов Azure, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките элемент content, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Получение ключей учетной записи хранения из Azure Resource Manager для обращения к хранилищу  

Теперь мы с помощью PowerShell обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к хранилищу. После получения ключа доступа к хранилищу можно вызвать операции отправки или скачивания в хранилище.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL-адрес чувствителен к регистру, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Проверьте, чтобы в resourceGroups обязательно использовался символ "G" (прописная буква). 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Затем нужно создать файл с именем "test.txt". Используйте ключ доступа к хранилищу для аутентификации с помощью командлета `New-AzStorageContent`, отправьте файл в контейнер больших двоичных объектов, а затем скачайте его.

```bash
echo "This is a test text file." > test.txt
```

Не забудьте сначала установить командлеты службы хранилища Azure с помощью `Install-Module Az.Storage`, а затем отправьте созданный большой двоичный объект с помощью командлета PowerShell `Set-AzStorageBlobContent`.

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Скачать отправленный большой двоичный объект с помощью командлета PowerShell `Get-AzStorageBlobContent`.

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать управляемое удостоверение, назначаемое системой, чтобы получить доступ к службе хранилища Azure с помощью ключа доступа.  Дополнительные сведения о ключах доступа к службе хранилища Azure см. здесь:

> [!div class="nextstepaction"]
>[Управление ключами доступа к хранилищу](/azure/storage/common/storage-create-storage-account)

