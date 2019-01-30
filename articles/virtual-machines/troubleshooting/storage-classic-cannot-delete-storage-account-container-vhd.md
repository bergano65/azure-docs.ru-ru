---
title: Устранение ошибок при удалении классических учетных записей хранения Azure, контейнеров или виртуальных жестких дисков | Документация Майкрософт
description: Устранение неполадок при удалении ресурсов хранилища, содержащих подключенные VHD.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 72493c6bba556314c3652be5251463d1d1e005bd
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383747"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Устранение ошибок при удалении классических ресурсов хранилища
В этой статье содержатся рекомендации по устранению неполадок при возникновении одной из приведенных ниже ошибок во время попытки удаления классической учетной записи хранения Azure, контейнера или VHD-файла страничного BLOB-объекта. 


В этой статье описаны проблемы, связанные только с классическими ресурсами хранилища. Если пользователь удаляет классическую виртуальную машину с помощью портала Azure, PowerShell или интерфейса командной строки, диски не удаляются автоматически. Пользователю предоставляется параметр для удаления ресурса "Диск". Если этот параметр не выбран, ресурс "Диск" не позволит удалить учетную запись хранения, контейнер и VHD-файл страничного BLOB-объекта.

Дополнительные сведения о дисках Azure см. [здесь](../../virtual-machines/windows/about-disks-and-vhds.md). Среда Azure предотвращает удаление диска, подключенного к виртуальной машине, чтобы избежать повреждения. Она также препятствует удалению контейнеров и учетных записей хранения, содержащих страничный BLOB-объект, который подключен к виртуальной машине. 

## <a name="what-is-a-disk"></a>Что такое ресурс "Диск"?
Ресурс "Диск" используется для подключения VHD-файла страничного BLOB-объекта к виртуальной машине в качестве диска операционной системы или диска данных. Ресурс диска операционной системы или диска данных до удаления будет продолжать арендовать VHD-файл. Ресурс хранилища, путь к которому показан на рисунке, нельзя будет удалить, если ресурс "Диск" указывает на него.

![Снимок экрана портала с открытой панелью "Свойства" диска (классического)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Действия для удаления классической виртуальной машины 
1. Удалите классическую виртуальную машину.
2. Если установлен флажок "Диски", **аренда диска** (как показано на рисунке выше), связанная с VHD-файлом страничного BLOB-объекта, будет недействительной. Сам VHD-файл страничного BLOB-объекта по-прежнему будет существовать в учетной записи хранения.
![Снимок экрана портала с открытой панелью ошибки удаления ("Delete") классической виртуальной машины](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. После прекращения аренды дисков можно удалить и страничные BLOB-объекты. Учетную запись хранения или контейнер можно удалить только после удаления всех содержащихся в них ресурсов "Диск".

>[!NOTE] 
>Если пользователь удалит виртуальную машину, но не удалит виртуальный жесткий диск, с него и дальше будет взиматься плата за использование хранилища, а именно за VHD-файл страничного BLOB-объекта. Плата взимается в зависимости от типа учетной записи хранения. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/en-us/pricing/details/storage/). Если пользователь больше не планирует использовать виртуальные жесткие диски, удалите их, чтобы избежать расходов в будущем. 

## <a name="unable-to-delete-storage-account"></a>Не удается удалить учетную запись хранения 

Если пользователь пытается удалить классическую учетную запись хранения, которая больше не используется, может наблюдаться следующее поведение.

#### <a name="azure-portal"></a>Портал Azure 
Пользователь переходит к классической учетной записи хранения на [портале Azure](https://portal.azure.com) и нажимает кнопку **удаления**. Отображается показанное ниже сообщение. 

С перечнем дисков, подключенных к виртуальной машине

![Снимок экрана портала с открытой панелью ошибки удаления ("Delete") классической виртуальной машины](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


С перечнем дисков, отключенных от виртуальной машины

![Снимок экрана портала с открытой панелью классической виртуальной машины без ошибок удаления ("Delete")](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Пользователь пытается удалить учетную запись хранения, которая больше не используется, с помощью классических командлетов PowerShell. Отображается следующее сообщение:

><span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>

><span style="color:red">Remove-AzureStorageAccount : BadRequest: В учетной записи хранения myclassicaccount есть несколько активных образов или дисков, например  
myclassicaccount. Убедитесь, что эти образы или диски удалены, перед тем как удалять эту учетную запись хранения.</span>

## <a name="unable-to-delete-storage-container"></a>Не удается удалить контейнер хранилища

Если пользователь пытается удалить классический контейнер хранения BLOB-объектов, который больше не используется, может наблюдаться следующее поведение.

#### <a name="azure-portal"></a>Портал Azure 
Пользователь не сможет удалить контейнер на портале Azure, если аренда для ресурсов "Диск" указывает на VHD-файл страничного BLOB-объекта в этом контейнере. Это сделано специально, чтобы избежать случайного удаления VHD-файлов и вместе с ними аренды для ресурсов "Диск". 

![Снимок экрана портала с открытой панелью списка контейнеров хранилища](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Если пользователь решит удалить контейнер с помощью PowerShell, появится следующая ошибка. 

><span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>

><span style="color:red">Remove-AzureStorageContainer : The remote server returned an error: (412) There is currently a lease on the container and no lease ID was specified in the request. (Сейчас контейнер находится в аренде, и в запросе не указан идентификатор аренды). Код состояния HTTP: 412 — Сообщение об ошибке HTTP: There is currently a lease on the container and no lease ID was specified in the request. (Сейчас контейнер находится в аренде, и в запросе не указан идентификатор аренды).</span>

## <a name="unable-to-delete-a-vhd"></a>Не удается удалить виртуальный жесткий диск 

После удаления виртуальной машины Azure пользователь пытается удалить VHD-файл (страничный BLOB-объект) и получает следующее сообщение:

#### <a name="azure-portal"></a>Портал Azure 
В зависимости от списка BLOB-объектов, выбранных для удаления, интерфейс портала будет выглядеть, как показано на одном из рисунков ниже.

1. Если выбраны только арендуемые BLOB-объекты, кнопка удаления не отображается.
![Снимок экрана портала с открытой панелью списка BLOB-объектов в контейнере](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Если выбрано сочетание арендуемых и доступных BLOB-объектов, отобразится кнопка удаления. При этом операция удаления не затрагивает страничные BLOB-объекты, арендуемые ресурсами "Диск". 
![Снимок экрана портала с открытой панелью списка BLOB-объектов в контейнере](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Снимок экрана портала с открытой панелью удаления выбранного BLOB-объекта](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Если пользователь решит удалить контейнер с помощью PowerShell, появится следующая ошибка. 

><span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>

><span style="color:red">Remove-AzureStorageBlob : The remote server returned an error: (412) There is currently a lease on the blob and no lease ID was specified in the request. (Сейчас BLOB-объект находится в аренде, и в запросе не указан идентификатор аренды). Код состояния HTTP: 412 — Сообщение об ошибке HTTP: There is currently a lease on the blob and no lease ID was specified in the request. (Сейчас BLOB-объект находится в аренде, и в запросе не указан идентификатор аренды).</span>


## <a name="resolution-steps"></a>Способы устранения

### <a name="to-remove-classic-disks"></a>Чтобы удалить классические диски
На портале Azure сделайте следующее:
1.  Перейдите на [портал Azure](https://portal.azure.com).
2.  Перейдите в раздел "Диски (классические)". 
3.  Перейдите на вкладку "Диски". ![Снимок экрана портала с открытой панелью списка BLOB-объектов в контейнере](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Выберите диск данных, а затем нажмите кнопку "Удалить диск".
 ![Снимок экрана портала с открытой панелью списка BLOB-объектов в контейнере](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Повторите операцию удаления, которую не удавалось выполнить ранее.
6.  Невозможно удалить учетную запись хранения или контейнер, пока они связаны хотя бы с одним диском.

### <a name="to-remove-classic-images"></a>Чтобы удалить классические образы   
На портале Azure сделайте следующее:
1.  Перейдите на [портал Azure](https://portal.azure.com).
2.  Перейдите в раздел "Образы ОС (классические)".
3.  Удалите образ.
4.  Повторите операцию удаления, которую не удавалось выполнить ранее.
5.  Невозможно удалить учетную запись хранения или контейнер, пока они связаны хотя бы с одним образом.
