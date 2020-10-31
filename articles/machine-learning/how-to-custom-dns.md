---
title: Использовать пользовательский DNS-сервер
titleSuffix: Azure Machine Learning
description: Как настроить пользовательский DNS-сервер для работы с Машинное обучение Azure рабочей областью и частной конечной точкой.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b16c8873a1778b907b288486c204d74ee31683cb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097963"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Использование рабочей области с пользовательским DNS-сервером

При использовании Машинное обучение Azure с виртуальной сетью существует [несколько способов обработки разрешения имен DNS](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances). По умолчанию Azure автоматически обрабатывает разрешение имен для рабочей области и частной конечной точки. Однако __при использовании собственного пользовательского DNS-сервера__ необходимо вручную создать записи DNS для рабочей области.

> [!IMPORTANT]
> В этой статье описывается, как найти полное доменное имя и IP-адреса для этих записей, которые не предоставляют сведений о настройке записей DNS для этих элементов. Сведения о добавлении записей см. в документации по программному обеспечению DNS.

## <a name="prerequisites"></a>Предварительные требования

- Виртуальная сеть Azure, использующая [собственный DNS-сервер](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

- Рабочая область Машинное обучение Azure с частной конечной точкой. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Знакомство с использованием [сетевой изоляции во время обучения & вывода](how-to-enable-virtual-network.md).

- При необходимости [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) или [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Поиск IP-адресов

Следующий список содержит полные доменные имена (FQDN), используемые рабочей областью и частной конечной точкой.

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* При создании вычислительного экземпляра необходимо также добавить запись для `<instance-name>.<region>.instances.azureml.ms` с частным IP-адресом частной конечной точки рабочей области. Обратите внимание, что доступ к вычислительному экземпляру можно получить только в пределах виртуальной сети.

Чтобы найти внутренние IP-адреса для полных доменных имен в виртуальной сети, используйте один из следующих методов.

> [!NOTE]
> Полные доменные имена и IP-адреса будут отличаться в зависимости от конфигурации. Например, значение GUID в имени домена будет соответствовать вашей рабочей области.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Портал Azure](#tab/azure-portal)

1. В [портал Azure](https://portal.azure.com)выберите __рабочую область__ машинное обучение Azure.
1. В разделе __Параметры__ выберите __частные конечные точки подключения__ .
1. Выберите ссылку в столбце __закрытая конечная точка__ , который отображается.
1. Список полных доменных имен (FQDN) и IP-адреса для частной конечной точки рабочей области находятся в нижней части страницы.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Список полных доменных имен на портале":::

---

Сведения, возвращаемые из всех методов, одинаковы. список полного доменного имени и частного IP-адреса для ресурсов.

| Полное доменное имя. | IP-адрес |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Некоторые полные доменные имена не отображаются в списке частной конечной точки, но являются обязательными для рабочей области. Эти полные доменные имена перечислены в следующей таблице и также должны быть добавлены на DNS-сервер.
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Если у вас есть вычислительный экземпляр, используйте `<instance-name>.<region>.instances.azureml.ms` , где `<instance-name>` — имя вычислительного экземпляра. Используйте частный IP-адрес закрытой конечной точки рабочей области. Обратите внимание, что доступ к вычислительному экземпляру можно получить только в пределах виртуальной сети.
>
> Для всех этих IP-адресов используйте тот же адрес, что и для `*.api.azureml.ms` записей, возвращенных предыдущими шагами.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Машинное обучение Azure с виртуальной сетью см. в [обзоре виртуальной сети](how-to-network-security-overview.md).
