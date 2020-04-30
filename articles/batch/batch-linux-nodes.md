---
title: Выполнение вычислительных узлов виртуальных машин под управлением Linux в пакетной службе Azure | Документация Майкрософт
description: Узнайте, как обрабатывать параллельные вычислительные рабочие нагрузки в пулах виртуальных машин Linux в пакетной службе Azure.
ms.topic: article
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7abdab248a6f19ec4d57018d65f883fdc838da21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116797"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Подготовка вычислительных узлов Linux в пулах пакетной службы

Пакетная служба Azure позволяет выполнять параллельные вычислительные рабочие нагрузки на виртуальных машинах Linux и Windows. В этой статье описывается создание пулов вычислительных узлов Linux в пакетной службе с помощью клиентских библиотек [Python][py_batch_package] и [.NET][api_net].

> [!NOTE]
> Пакеты приложений поддерживаются во всех пулах пакетной службы, созданных после 5 июля 2017 г. Если пул создан с помощью конфигурации облачной службы, пакеты приложений также поддерживаются в пулах пакетной службы, созданных между 10 марта 2016 г. и 5 июля 2017 г. Пулы пакетной службы, созданные до 10 марта 2016 г., не поддерживают пакеты приложений. Дополнительные сведения о развертывании приложений на узлах пакетной службы с помощью пакетов приложений см .в [этой статье](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Конфигурация виртуальной машины
При создании пула вычислительных узлов в пакетной службе есть два варианта для выбора размера узла и операционной системы: Cloud Services Configuration (Конфигурация облачных служб) и "Конфигурация виртуальной машины".

**Cloud Services Configuration** (Конфигурация облачных служб) предоставляет *только*вычислительные узлы Windows. Доступные размеры вычислительных узлов перечислены в статье [Размеры для облачных служб](../cloud-services/cloud-services-sizes-specs.md), а доступные операционные системы — в статье [Таблица совместимости выпусков гостевых ОС Azure и пакетов SDK](../cloud-services/cloud-services-guestos-update-matrix.md). При создании пула, содержащего узлы облачных служб Azure, необходимо указать размер узла и "семейство ОС", которые описаны в упомянутых ранее статьях. Для пулов вычислительных узлов Windows чаще всего используются облачные службы.

**Virtual Machine Configuration** предоставляет образы Windows и Linux для вычислительных узлов. Доступные размеры вычислительных узлов перечислены в статьях [Размеры виртуальных машин в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) и [Размеры виртуальных машин в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). При создании пула, содержащего узлы конфигурации виртуальных машин, необходимо указать размер узлов, ссылку на образ виртуальной машины и номер SKU агента узла пакетной службы для установки на узлах.

### <a name="virtual-machine-image-reference"></a>Ссылка на образ виртуальной машины

Для предоставления вычислительных узлов в конфигурации виртуальной машины пакетная служба использует [масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Вы можете указать образ из [Azure Marketplace][vm_marketplace] или подготовленный пользовательский образ. Дополнительные сведения о пользовательских образах см. в статье [Создание пула с помощью коллекции общих образов](batch-sig-images.md).

При настройке ссылки на образ виртуальной машины задаются свойства образа виртуальной машины. Приведенные ниже свойства являются обязательными при создании ссылки на образ виртуальной машины.

| **Свойства ссылки на образ** | **Пример** |
| --- | --- |
| Издатель |Canonical |
| Предложение |UbuntuServer |
| номер SKU |18.04-LTS |
| Версия |последняя |

> [!TIP]
> Дополнительные сведения об этих свойствах и о том, как получить список образов из Marketplace, см. в статье [Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Обратите внимание, что не все образы из Marketplace в настоящее время совместимы с пакетной службой. Дополнительные сведения см. в разделе [Номер SKU агента узла](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Номер SKU агента узла
Агент узла пакетной службы — это программа, которая выполняется на каждом узле в пуле и предоставляет интерфейс контроля и управления между узлом и пакетной службой. Существуют различные реализации агента узла, известные как номера SKU, для различных операционных систем. По существу, при создании конфигурации виртуальной машины сначала следует указать ссылку на образ виртуальной машины, а затем указать агент узла, который будет установлен на этом образе. Как правило, каждый номер SKU агента узла совместим с несколькими образами виртуальных машин. Вот несколько примеров номеров SKU агентов узлов:

* Пакетная службы. Node. Ubuntu 18,04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Не все образы виртуальных машин, доступные в Marketplace, совместимы с доступными на данный момент агентами узлов пакетной службы. Для получения списка доступных номеров SKU агентов узлов и образов виртуальных машин, с которыми они совместимы, используйте пакеты SDK пакетной службы. Дополнительные сведения и примеры получения списка допустимых образов в среде выполнения см. далее в этой статье в разделе [Список образов виртуальных машин](#list-of-virtual-machine-images).
>
>

## <a name="create-a-linux-pool-batch-python"></a>Создание пула Linux: библиотека Python для пакетной службы
В следующем фрагменте кода показан пример создания пула вычислительных узлов Ubuntu Server с помощью [клиентской библиотеки пакетной службы Microsoft Azure для Python][py_batch_package]. Справочную документацию по модулю Python для пакетной службы можно найти в [пакете Azure. Batch][py_batch_docs] по чтению документации.

В этом фрагменте кода явно создается объект [ImageReference][py_imagereference] и указывается каждое из его свойств (publisher, offer, SKU, version). Однако в рабочем коде рекомендуется использовать метод [list_supported_images][py_list_supported_images] для определения и выбора из доступных комбинаций образа и номера SKU агента узла во время выполнения.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Как упоминалось ранее, вместо того, чтобы создавать [ImageReference][py_imagereference] явным образом, рекомендуется использовать метод [list_supported_images][py_list_supported_images] , чтобы динамически выбирать из поддерживаемых в настоящее время комбинаций образа или агента узла. Следующий фрагмент кода Python демонстрирует, как использовать этот метод.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Создание пула Linux: библиотека .NET для пакетной службы
В следующем фрагменте кода показан пример создания пула вычислительных узлов Ubuntu Server с помощью клиентской библиотеки [.NET для пакетной службы][nuget_batch_net]. [Справочную документацию по .NET для пакетной службы][api_net] можно найти на сайте docs.microsoft.com.

В следующем фрагменте кода используется [PoolOperations][net_pool_ops]. Метод [листсуппортедимажес][net_list_supported_images] для выбора из списка поддерживаемых в настоящее время образов Marketplace и номеров SKU агента узла. Этот способ является предпочтительным, так как список поддерживаемых сочетаний может меняться время от времени. Чаще всего добавляются поддерживаемые сочетания.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Несмотря на то, что предыдущий фрагмент использует [PoolOperations][net_pool_ops]. Метод [листсуппортедимажес][net_list_supported_images] для динамического перечисления и выбора из поддерживаемых сочетаний образов и номеров SKU агента узла (рекомендуется) можно также настроить [ImageReference][net_imagereference] явным образом:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Список образов виртуальных машин
Чтобы получить список всех поддерживаемых образов виртуальных машин Marketplace для пакетной службы и соответствующих агентов узлов, используйте [list_supported_images][py_list_supported_images] (Python), [листсуппортедимажес][net_list_supported_images] (пакет .NET) или соответствующий API в соответствующем языковом пакете SDK, который вы выбрали.

## <a name="connect-to-linux-nodes-using-ssh"></a>Подключение к узлам Linux с помощью SSH
Во время разработки или устранения неполадок может потребоваться войти на узлы в пуле. В отличие от вычислительных узлов Windows, для подключения к узлам Linux нельзя использовать протокол удаленного рабочего стола (RDP). Вместо этого пакетная служба включает доступ по протоколу SSH на каждом узле для удаленного подключения.

В следующем фрагменте кода Python создается пользователь на каждом узле пула, который необходим для удаленного подключения. Затем выводятся сведения о подключении SSH для каждого узла.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Ниже приведен пример выходных данных вышеупомянутого кода для пула, содержащего четыре узла Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

При создании пользователя на узле вместо пароля можно указать открытый ключ SSH. В пакете SDK для Python используйте параметр **ssh_public_key** в [ComputeNodeUser][py_computenodeuser]. В среде .NET используйте свойство [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## <a name="pricing"></a>Цены
Пакетная служба Azure основана на технологии виртуальных машин Azure и облачных служб Azure. Сама Пакетная служба предоставляется бесплатно. Это означает, что плата взимается только за ресурсы вычислений (и связанные с этим затраты), которые используются в решениях пакетной службы. При выборе режима **Cloud Services Configuration** (Конфигурация облачных служб) плата взимается в зависимости от структуры [цен на облачные службы][cloud_services_pricing]. При выборе режима **Конфигурация виртуальной машины** плата взимается в зависимости от структуры [цен на виртуальные машины][vm_pricing].

При развертывании приложений на узлах пакетной службы с помощью [пакетов приложений](batch-application-packages.md) также взимается плата за ресурсы службы хранилища Azure, используемые пакетами приложений.

## <a name="next-steps"></a>Дальнейшие шаги

[Примеры кода Python][github_samples_py] в репозитории [azure-batch-samples][github_samples] на портале GitHub содержат сценарии, в которых показано, как выполнять распространенные пакетные операции, такие как создание пула, задания и задачи. [Файл сведений][github_py_readme], прилагаемый к примерам кода Python, содержит подробные сведения об установке необходимых пакетов.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
