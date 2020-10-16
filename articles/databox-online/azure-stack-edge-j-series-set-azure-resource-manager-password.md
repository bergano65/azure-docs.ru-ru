---
title: Установка Azure Resource Manager пароля на устройстве Azure Stack с графическим процессором Pro
description: В этой статье описывается, как подключиться к Azure Resource Manager, работающей на графическом процессоре Azure Stack ребра Pro, с помощью Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: df5fea8101834dae089ab97354c438363321a707
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904480"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Задание Azure Resource Manager пароля для устройства GPU Pro на Azure Stack

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

В этой статье описывается, как задать пароль Azure Resource Manager. Этот пароль необходимо задать при подключении к локальным API-интерфейсам устройства через Azure Resource Manager.

Процедура установки пароля может отличаться в зависимости от того, используется портал Azure или командлеты PowerShell. Каждая из этих процедур описана в следующих разделах.


## <a name="reset-password-via-the-azure-portal"></a>Сброс пароля с помощью портал Azure

1. В портал Azure перейдите к ресурсу Azure Stackного периметра, созданному для управления устройством. Поочередно выберите **Вычисления Edge > Начало работы**.

2. В области справа на панели команд выберите **сбросить пароль ГРАНИЧНОЙ ARM**. 

    ![Сброс пароля пользователя Еджеарм 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. В колонке **Сброс пароля пользователя еджеарм** введите пароль для подключения к локальным API-интерфейсам устройства через Azure Resource Manager. Подтвердите пароль и нажмите кнопку **сбросить**.

    ![Сброс пароля пользователя Еджеарм 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Сброс пароля с помощью PowerShell

1. На портале Azure перейдите к ресурсу Azure Stack ребра, созданному для управления устройством. Запишите следующие параметры на странице **Обзор** .

    - Имя ресурса Azure Stack ребра
    - Идентификатор подписки

2. Последовательно выберите **параметры > свойства**. Запишите следующие параметры на странице **свойств** .

    - Группа ресурсов
    - Ключ шифрования ЦИК: выберите представление, а затем скопируйте **ключ шифрования**.

    ![Получение ключа шифрования ЦИК](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Укажите пароль, который будет использоваться для подключения к Azure Resource Manager.

4. Запустите Cloud Shell. Щелкните значок в правом верхнем углу.

    ![Запуск Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    После запуска Cloud Shell может потребоваться переключиться в PowerShell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Задать контекст. Тип:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Пример выходных данных:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Если у вас есть старые модули PS, их необходимо установить.

    `Remove-Module  Az.DataBoxEdge -force`

    Ниже приведен пример выходных данных. В этом примере нет старых модулей для установки.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Следующий набор команд загрузит и запустит сценарий для установки модулей PowerShell.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. В следующем наборе команд необходимо указать имя ресурса, имя группы ресурсов, ключ шифрования и пароль, указанный на предыдущем шаге.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    Параметры пароля и ключа шифрования должны передаваться как защищенные строки. Используйте следующие командлеты для преобразования пароля и ключа шифрования в защищенные строки.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Используйте приведенные выше защищенные строки в качестве параметров в командлете Set-AzDataBoxEdgeUser, чтобы сбросить пароль. Используйте ту же группу ресурсов, которая использовалась при создании ресурса Azure Stack ребра Pro/Шлюз Data Box.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Ниже приведен пример выходных данных.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Используйте новый пароль для подключения к Azure Resource Manager.

## <a name="next-steps"></a>Дальнейшие шаги

[Подключение к Azure Resource Manager.](azure-stack-edge-j-series-connect-resource-manager.md)
