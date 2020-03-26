---
title: Руководство. Создание кластера виртуальных машин Azure с помощью Terraform и реестра модулей
description: Из этого руководства вы узнаете, как использовать модули Terraform для создания кластера виртуальных машин Windows в Azure
keywords: Azure DevOps, Terraform, виртуальная машина, кластер виртуальных машин, реестр модулей
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945286"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Руководство по Создание кластера виртуальных машин Azure с помощью Terraform и реестра модулей

В этой статье описывается создание небольшого кластера виртуальных машин с помощью [модуля вычислений Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) Terraform. Из этого руководства вы узнаете, как выполнить следующие задачи: 

> [!div class="checklist"]
> * настройка аутентификации в Azure;
> * создание шаблона Terraform;
> * визуализация изменений с планом;
> * применение конфигурации для создания кластера виртуальных машин.

Дополнительные сведения о Terraform см. в [документации по Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>настройка аутентификации в Azure;

> [!TIP]
> При [использовании переменной среды Terraform](terraform-install-configure.md) или выполнении этого руководства в [Azure Cloud Shell](/azure/cloud-shell/overview) пропустите этот шаг.

 Просмотрите статью об [установке Terraform и настройке доступа к Azure](terraform-install-configure.md), чтобы создать субъект-службу Azure. Используйте этот субъект-службу для заполнения нового файла `azureProviderAndCreds.tf` в пустом каталоге с помощью следующего кода:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Создание шаблона

Создайте шаблон Terraform с именем `main.tf` со следующим кодом:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Выполните `terraform init` в каталоге конфигурации. При использовании версии Terraform не менее 0.10.6 будут показаны следующие выходные данные:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>визуализация изменений с планом;

Выполните команду `terraform plan` для предварительного просмотра инфраструктуры виртуальных машин, созданной шаблоном.

![План Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Создание виртуальных машин с применением

Запустите `terraform apply` для подготовки виртуальных машин в Azure.

![Применение Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Просмотрите список модулей Azure Terraform](https://registry.terraform.io/modules/Azure)