---
title: Выполнять операции с хранилищем очередей Azure с помощью PowerShell, службу хранилища Azure
description: Как выполнять операции с хранилищем очередей Azure при помощи PowerShell
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: db366fea96967559c65559864ff8e367fa12ad65
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142590"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Выполнение операций хранилища очередей Azure с помощью Azure PowerShell

Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ по протоколам HTTP или HTTPS практически из любой точки мира. Дополнительные сведения см. в статье [Общие сведения об очередях](storage-queues-introduction.md). В этом практическом руководстве рассматриваются распространенные операции с хранилищем очередей. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание очереди
> * Извлечение очереди
> * Добавление сообщения
> * Чтение сообщения
> * Удаление сообщения
> * Удаление очереди

Для работы с этим практическим руководством требуется модуль Azure PowerShell Az 0.7 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps).

В PowerShell нет командлетов плоскости данных для очередей. Чтобы выполнять операции плоскости данных, такие как добавление, чтение и удаление сообщений, необходимо использовать клиентскую библиотеку хранилища .NET, так как она предоставлена в PowerShell. Создайте объект сообщения и используйте команды, например AddMessage, для выполнения операций с этим сообщением. В этой статье показано, как это сделать.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Получение списка расположений

Если вы не знаете, какое расположение нужно использовать, можно получить список доступных расположений. Получив список, найдите расположение, которое нужно использовать. В этом задании будет использоваться **eastus**. Сохраните его в переменной **location** для использования в будущем.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Сохраните имя группы ресурсов в переменной для будущего использования. В этом примере создается группа ресурсов с именем *howtoqueuesrg* в регионе *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Создать учетную запись хранения

Создайте стандартную учетную запись хранения общего назначения с локально избыточным хранилищем (LRS) с помощью команды [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Получите контекст учетной записи хранения, определяющий необходимую учетную запись хранения. Действуя в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Создание очереди

Сначала в этом примере устанавливается соединение со службой хранилища Azure, используя контекст учетной записи хранения, который включает имя учетной записи хранения и ее ключ доступа. Затем вызывается командлет [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue), чтобы создать очередь с именем queuename.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Сведения о соглашениях об именовании для службы очередей Azure см. в статье [Именование очередей и метаданных](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Извлечение очереди

Можно запрашивать и получать указанную очередь или список всех очередей в учетной записи хранения. В следующих примерах демонстрируется извлечение всех очередей в учетной записи хранения и определенной очереди. Обе команды используют командлет [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue).

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Добавление сообщения в очередь

Для операций с фактическими сообщениями в очереди используйте клиентскую библиотеку хранилища .NET, предоставленную в PowerShell. Чтобы добавить сообщение в очередь, создайте новый экземпляр объекта сообщения, [Microsoft.Azure.Storage.Queue.CloudQueueMessage](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.-ctor?redirectedfrom=MSDN&view=azure-dotnet#Microsoft_WindowsAzure_Storage_Queue_CloudQueueMessage__ctor_System_Byte___) класса. Затем вызовите метод [AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Для создания CloudQueueMessage можно использовать строку (в формате UTF-8) или массив байтов.

В следующем примере демонстрируется добавление сообщений в очередь.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName "Microsoft.Azure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Если вы используете [Обозреватель службы хранилища Azure](https://storageexplorer.com), можно подключиться к учетной записи хранения Azure и вывести на экран содержащиеся в ней очереди, а также детализировать одну из них, чтобы просмотреть сообщения в ней. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Считывание сообщения из очереди и его удаление

Сообщения считываются в порядке их поступления. Это не гарантируется. При считывании сообщения из очереди оно становится невидимым для других процессов, просматривающих очередь. Это позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку.  

Это **время ожидания невидимости** определяет, как долго сообщение остается невидимым, прежде чем снова станет доступным для обработки. По умолчанию это 30 секунд. 

Ваш код считывает сообщение из очереди в два этапа. При вызове [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?redirectedfrom=MSDN&view=azure-dotnet#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_GetMessage_System_Nullable_System_TimeSpan__Microsoft_WindowsAzure_Storage_Queue_QueueRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) метод, вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage** , становится невидимым для другого кода, считывающего сообщения из этой очереди. Чтобы завершить удаление сообщения из очереди, вызовите [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?redirectedfrom=MSDN&view=azure-dotnet#overloads) метод. 

В следующем примере можно прочитать три сообщения в очереди, после чего следует время ожидания 10 секунд (время ожидания невидимости). Затем можно снова прочитать три сообщения и удалить их после прочтения, вызвав **DeleteMessage**. При попытке чтения очереди после удаления сообщений $queueMessage будет возвращаться как значение NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Удаление очереди

Для удаления очереди и всех сообщений, содержащихся в ней, вызовите командлет Remove-AzStorageQueue. В следующем примере показан способ удаления указанной очереди с использованием командлета Remove-AzStorageQueue.

```powershell
# Delete the queue 
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этом упражнении, удалите группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление созданной учетной записи хранения и самой группы ресурсов.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого практического руководства вы узнали о базовом управлении хранилищем очередей с помощью PowerShell, включая выполнение следующих задач:

> [!div class="checklist"]
> * Создание очереди
> * Извлечение очереди
> * Добавление сообщения
> * Чтение следующего сообщения
> * Удаление сообщения 
> * Удаление очереди

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Командлеты Microsoft Azure PowerShell для службы хранилища

* [Командлеты PowerShell для службы хранилища](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Обозреватель службы хранилища Microsoft Azure

* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
