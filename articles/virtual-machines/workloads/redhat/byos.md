---
title: Red Hat Enterprise Linux принесите собственные подписные изображения Azure Документы Майкрософт
description: Узнайте о том, как можно ознакомиться с изображениями собственной подписки для Red Hat Enterprise Linux на Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264600"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux приносит в Azure золотые изображения

Изображения Red Hat Enterprise Linux (RHEL) доступны в Azure с помощью модели с оплатой по мере использования или принесите собственную подписку (BYOS) (Red Hat Gold Image). В этой статье представлен обзор золотых изображений Red Hat в Azure.

>[!NOTE]
> RhEL BYOS Gold Images доступны в облаках Azure Public (коммерческие) и Azure Government. Они недоступны в облаках Azure China или Azure Blackforest.

## <a name="important-points-to-consider"></a>Важные моменты для рассмотрения

- Красные Шляпы Золотые изображения, представленные в этой программе готовых к производству изображений RHEL похож на RHEL платить по мере вас-го изображения в Azure Marketplace.
- Изображения соответствуют текущим политикам, описанным в [изображениях Red Hat Enterprise Linux на Azure.](./redhat-images.md)
- Стандартные политики поддержки применяются к вд, созданные на этих изображениях.
- VMs, подготовленные из Red Hat Gold Images, не несут сборов RHEL, связанных с изображениями RHEL с оплатой по мере продажи.
- Изображения не имеют права. Вы должны использовать Red Hat Подписка-менеджер, чтобы зарегистрироваться и подписаться на вс-голов, чтобы получить обновления от Red Hat непосредственно.
- В настоящее время невозможно динамически переключаться между BYOS и платежными моделями для Linux-изображений. Чтобы переключить модель выставления счетов, необходимо передислоцировать VM из соответствующего изображения.

>[!NOTE]
> Поколение 2 RHEL BYOS изображения в настоящее время не доступны через предложение рынка. Если вам требуется изображение Generation 2 RHEL BYOS, посетите панель мониторинга облачного доступа в управлении подпиской Red Hat. Для получения дополнительной [информации](https://access.redhat.com/articles/4847681)см.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Требования и условия для доступа к Red Hat Gold Images

1. Познакомитесь с условиями [программы Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Включите подписку Red Hat на облачный доступ в [подписке Red Hat.](https://access.redhat.com/management/cloud) Необходимо иметь под рукой подписки Azure, которые будут зарегистрированы для Cloud Access.

1. Если подписки Red Hat, включенные для Cloud Access, отвечают требованиям, ваши подписки Azure автоматически включены для доступа к Золотому Изображению.

### <a name="expected-time-for-image-access"></a>Ожидаемое время доступа к изображению

После завершения шагов включения в Облачный доступ Red Hat проверяет ваше право на получение золотых изображений Red Hat. Если проверка будет успешной, вы получаете доступ к золотым изображениям в течение трех часов.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Используйте золотые изображения Red Hat с портала Azure

1. После того, как подписка Azure получит доступ к Red Hat Gold Images, вы можете найти их на [портале Azure.](https://portal.azure.com) Перейти к **созданию ресурса** > **Просмотреть все**.

1. В верхней части страницы вы увидите, что у вас есть частные предложения.

    ![Рынок частных предложений](./media/rhel-byos-privateoffers.png)

1. Выберите фиолетовую ссылку или прокрутите вниз в нижней части страницы, чтобы увидеть ваши частные предложения.

1. Остальная часть подготовки в uI ничем не отличается от любого другого существующего изображения Red Hat. Выберите версию RHEL и следуйте подсказкам, чтобы предоставить ваш VM. Этот процесс также позволяет принять условия изображения на заключительном этапе.

>[!NOTE]
>Эти шаги до сих пор не позволит ваш Red Hat Золотое изображение для программного развертывания. Требуется дополнительный шаг, как описано в разделе "Дополнительная информация".

Остальная часть этого документа посвящена методу CLI для предоставления и принятия условий на изображении. UI и CLI полностью взаимозаменяемы в том, что касается конечного результата (подготовленного RHEL Gold Image VM).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Используйте золотые изображения Red Hat из Azure CLI

Следующие инструкции проходят через начальный процесс развертывания для RHEL VM с помощью Azure CLI. Эти инструкции предполагают, что у вас [установлен Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

>[!IMPORTANT]
>Убедитесь, что вы используете все буквы нижнего регистра в издателе, предлагают, планируют и изображения ссылки для всех следующих команд.

1. Убедитесь, что вы находитесь в желаемой подписке.

    ```azurecli
    az account show -o=json
    ```

1. Создайте группу ресурсов для вашего Изображения Red Hat Gold Image VM.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Примите термины изображения.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Эти условия должны быть приняты *один раз за подписку Azure, на изображение SKU*.

1. (Необязательно) Проверка развертывания VM со следующей командой:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Предоставьте vM, запустив ту же команду, что и показано в предыдущем примере, без аргумента. `--validate`

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH в ваш VM, и убедитесь, что у вас есть неозаглавлена изображения. Для этого шага `sudo yum repolist`запустите. Для RHEL 8, используйте `sudo dnf repolist`. Выход просит вас использовать подписку-менеджер для регистрации VM с Red Hat.

>[!NOTE]
>На RHEL `dnf` 8, и `yum` являются взаимозаменяемыми. Для получения дополнительной [информации](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)см.

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Используйте красные шляпы Золотые изображения от PowerShell

Следующий скрипт — это пример. Замените группу ресурсов, местоположение, имя VM, информацию о входе и другие переменные конфигурацией по вашему выбору. Информация об издателе и плане должна быть ниже.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Шифрование Red Hat Enterprise Linux принести свой собственный-подписки Золотые изображения

Red Hat Enterprise Linux BYOS Gold Images может быть обеспечена с помощью [шифрования azure Disk.](../../linux/disk-encryption-overview.md) Подписка *должна* быть зарегистрирована, прежде чем вы сможете включить шифрование. Для получения дополнительной информации о том, как зарегистрировать RHEL BYOS Золотое изображение, [см. Как зарегистрироваться и подписаться на систему на портале клиентов Red Hat с помощью Red Hat Подписка-менеджер](https://access.redhat.com/solutions/253273). Если у вас есть активная подписка Red Hat, вы также можете прочитать [Создание Red Hat ключей активации портала клиента.](https://access.redhat.com/articles/1378093)

Шифрование azure Disk не поддерживается на [пользовательских изображениях Red Hat.](../../linux/redhat-create-upload-vhd.md) Дополнительные требования к шифрованию дисков Azure и предпосылки задокументированы в [шифровании дисков Azure для Linux VMs.](../../linux/disk-encryption-overview.md#additional-vm-requirements)

Для того, чтобы применить шифрование azure Disk, можно ознакомиться со [сценариями шифрования дисков Azure на Linux VMs](../../linux/disk-encryption-linux.md) и связанных с ними статьях.

## <a name="additional-information"></a>Дополнительные сведения

- Если вы попытаетесь предоставить VM подписку, которая не включена для этого предложения, вы получите следующее сообщение:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    В этом случае свяжитесь с корпорацией Майкрософт или Red Hat, чтобы включить подписку.

- При изменении снимка с изображения RHEL BYOS и попытка опубликовать это пользовательское изображение в [Общей галерее изображений,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)необходимо предоставить информацию о плане, которая соответствует исходному источнику снимка. Например, команда может выглядеть следующим образом:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Обратите внимание на параметры плана в финальной строке.

    [Шифрование azure Disk](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) не поддерживается на пользовательских изображениях.

- Если вы используете автоматизацию для предоставления VMs из изображений RHEL BYOS, необходимо предоставить параметры плана, аналогичные тем, которые были показаны в командах выборки. Например, если вы используете Terraform, вы предоставляете информацию о плане в [блоке плана.](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)

## <a name="next-steps"></a>Дальнейшие действия

- Для пошаговых руководств и деталей программы для Cloud Access можно [ознакомиться с документацией Red Hat Cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Чтобы узнать больше об инфраструктуре обновления Red Hat, смотрите [инфраструктуру обновления Azure Red Hat.](./redhat-rhui.md)
- Чтобы узнать больше обо всех изображениях Red Hat в Azure, смотрите [страницу документации](./redhat-images.md).
- Для получения информации о политиках поддержки Red Hat для всех версий RHEL можно ознакомиться на странице [жизненного цикла Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Для получения дополнительной документации по RHEL Gold Images см. [Red Hat documentation](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)
