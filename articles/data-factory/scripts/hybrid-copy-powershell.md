---
title: 'Скрипт PowerShell: копирование данных из локальной среды в Azure с помощью Фабрики данных | Документация Майкрософт'
description: Этот сценарий PowerShell копирует данные из локальной базы данных SQL Server в другое хранилище BLOB-объектов Azure.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 1218408bee067d6edb274fdcbf4fae62b8245a01
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160668"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Создание конвейера фабрики данных для копирования данных из локальной среды в Azure с помощью PowerShell

Этот пример сценария PowerShell создает конвейер в фабрике данных Azure, который копирует данные локальной базы данных SQL Server в другое в хранилище BLOB-объектов Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Технические условия

- **SQL Server.** В этом примере в качестве **исходного** хранилища данных используется локальная база данных SQL Server.
- **Учетная запись хранения Azure.** В этом примере в качестве **места назначения и приемника** будет использоваться хранилище BLOB-объектов Azure. в статье [Об учетных записях хранения Azure](../../storage/common/storage-quickstart-create-account.md) .
- **Локальная среда выполнения интеграции**. Скачайте MSI-файл из [центра загрузки](https://www.microsoft.com/download/details.aspx?id=39717) и запустите его, чтобы установить локальную среду выполнения интеграции на компьютере.  

### <a name="create-sample-database-in-sql-server"></a>Создание примера базы данных в SQL Server
1. В локальной базе данных SQL Server создайте таблицу **emp**, используя следующий сценарий SQL. 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Вставьте в эту таблицу примеры данных.

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Пример скрипта

> [!IMPORTANT]
> Этот скрипт создает JSON-файлы, определяющие сущности фабрики данных (связанную службу, набор данных и конвейер) на жестком диске в папке c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Чтобы удалить фабрику данных из группы ресурсов, выполните следующую команду: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Создали фабрику данных. |
| [Новый AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Шифрует учетные данные в связанной службе и создает новое определение связанной службы с зашифрованными учетными данными. 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Создает в этой фабрике данных связанную службу. Связанная служба вычисляет или привязывает хранилище данных к фабрике данных. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Создает набор данных в фабрике данных. Набор данных представляет ввод или вывод для действия в конвейере. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Создает конвейер в фабрике данных. Конвейер содержит одно или несколько действий, выполняющих определенную операцию. В этом конвейере действие копирования копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. |
| [Вызвать AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Создает выполнение для конвейера. Другими словами, запускает конвейер. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Получает сведения о выполнении действия в конвейере. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для Azure Data Factory приведены [здесь](../samples-powershell.md).
