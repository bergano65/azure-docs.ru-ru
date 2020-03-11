---
title: Red Hat Enterprise Linux образы Azure с собственной подпиской | Документация Майкрософт
description: Узнайте о собственных образах подписки на Red Hat Enterprise Linux в Azure.
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
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970470"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux образы Gold собственных подписок в Azure

Образы Red Hat Enterprise Linux (RHEL) доступны в Azure с помощью модели с оплатой по мере использования или собственной подписки (BYOS) (образ Red Hat Gold). В этой статье представлен обзор образов Red Hat Gold в Azure.

>[!NOTE]
> Образы RHEL BYOS Gold доступны в общедоступных (коммерческих) и облачных облаках Azure. Они недоступны в Azure для Китая или в облаке Azure Блаккфорест.

## <a name="important-points-to-consider"></a>Важные моменты, которые следует учитывать

- Образы Red Hat Gold, представленные в этой программе, являются готовыми RHEL образами, аналогичными образами с оплатой по мере использования в Azure Marketplace.
- Образы соответствуют текущим политикам, описанным в [Red Hat Enterprise Linux образы в Azure](./redhat-images.md).
- Политики поддержки уровня "Стандартный" применяются к виртуальным машинам, созданным из этих образов.
- Виртуальные машины, подготовленные на основе образов Red Hat Gold, не несут RHEL плату, связанную с образами RHEL с оплатой по мере использования.
- Образы являются неуполномоченными. Вы должны использовать диспетчер подписки Red Hat для регистрации и подписки на виртуальные машины, чтобы получать обновления с Red Hat напрямую.
- Сейчас невозможно динамически переключаться между BYOS и моделями выставления счетов с оплатой по мере использования для образов Linux. Чтобы переключить модель выставления счетов, необходимо повторно развернуть виртуальную машину из соответствующего образа.

>[!NOTE]
> Образы поколения 2 RHEL BYOS в настоящее время недоступны в рамках предложения Marketplace. Если вам требуется образ поколения 2 RHEL BYOS, посетите панель мониторинга доступа к облаку в консоли управления подписками Red Hat. Дополнительные сведения см. в [документации по Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Требования и условия для доступа к образам Red Hat Gold

1. Знакомство с терминами [программы для облачного доступа Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Включите подписки Red Hat для облачного доступа в [диспетчере подписки Red Hat](https://access.redhat.com/management/cloud). Необходимо, чтобы подписки Azure, которые будут зарегистрированы для облачного доступа, были настроены вручную.

1. Если подписки Red Hat, которые вы включили для доступа к облаку, отвечают требованиям к допустимости, то подписки Azure автоматически включаются для доступа Gold Image.

### <a name="expected-time-for-image-access"></a>Ожидаемое время доступа к изображению

После завершения действий по включению облачного доступа Red Hat проверяет допустимость образов Red Hat Gold. Если проверка прошла успешно, вы получаете доступ к золотым изображениям в течение трех часов.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Используйте образы Red Hat Gold из портал Azure

1. После того как подписка Azure получит доступ к образам Red Hat Gold, их можно будет указать в [портал Azure](https://portal.azure.com). Перейдите к разделу **Создание ресурса** > **Просмотреть все**.

1. В верхней части страницы вы увидите, что у вас есть частные предложения.

    ![Частные предложения Marketplace](./media/rhel-byos-privateoffers.png)

1. Щелкните фиолетовое соединение или прокрутите страницу вниз до нижней части страницы, чтобы увидеть ваши частные предложения.

1. Остальная часть подготовки в пользовательском интерфейсе не отличается от других существующих изображений Red Hat. Выберите версию RHEL и следуйте инструкциям по подготовке виртуальной машины. Этот процесс также позволяет принять условия образа на последнем шаге.

>[!NOTE]
>Эти шаги до сих пор не включают образ Red Hat Gold для программного развертывания. Требуется дополнительный шаг, как описано в разделе "Дополнительные сведения".

Остальная часть этого документа посвящена методу CLI для предоставления и принятия условий на изображении. Пользовательский интерфейс и CLI полностью взаимозаменяемы, как и окончательный результат (подготовленная виртуальная машина образа RHEL Gold).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Используйте образы Red Hat Gold из Azure CLI

Приведенные ниже инструкции описывают процесс первоначального развертывания виртуальной машины RHEL с помощью Azure CLI. В этих инструкциях предполагается, что [Azure CLI установлен](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Обязательно используйте все строчные буквы на издателе, предложения, планы и ссылки на изображения для всех следующих команд.

1. Убедитесь, что вы находитесь в нужной подписке.

    ```azurecli
    az account show -o=json
    ```

1. Создайте группу ресурсов для виртуальной машины образа Red Hat Gold.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Примите условия образа.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Эти условия должны быть приняты *один раз в каждой подписке Azure на номер SKU образа*.

1. Используемых Проверьте развертывание виртуальной машины с помощью следующей команды:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Подготавливает виртуальную машину, выполнив ту же команду, которая указана в предыдущем примере без аргумента `--validate`.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Подключитесь к виртуальной машине по протоколу SSH и убедитесь в отсутствии образа. Чтобы выполнить этот шаг, выполните `sudo yum repolist`. Для RHEL 8 используйте `sudo dnf repolist`. В выходных данных будет предложено использовать диспетчер подписки для регистрации виртуальной машины с помощью Red Hat.

>[!NOTE]
>В RHEL 8 `dnf` и `yum` являются взаимозаменяемыми. Дополнительные сведения см. в разделе [руководств администратора RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Использование образов Red Hat Gold из PowerShell

Следующий скрипт — это пример. Замените группу ресурсов, расположение, имя виртуальной машины, сведения для входа и другие переменные с выбранной конфигурацией. Сведения о издателе и плане должны быть строчными.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Зашифровать Red Hat Enterprise Linux образы Gold с собственной подпиской

Для защиты образов Red Hat Enterprise Linux BYOS Gold можно использовать [Шифрование дисков Azure](../../linux/disk-encryption-overview.md). Для включения шифрования *необходимо* зарегистрировать подписку. Дополнительные сведения о том, как зарегистрировать образ RHEL BYOS Gold, см. в статье [Регистрация и подписка системы на портале клиента Red Hat с использованием диспетчера подписки Red Hat](https://access.redhat.com/solutions/253273). Если у вас есть активная подписка Red Hat, вы также можете ознакомиться с [созданием ключей активации для клиентских порталов Red Hat](https://access.redhat.com/articles/1378093).

Шифрование дисков Azure не поддерживается в [пользовательских образах Red Hat](../../linux/redhat-create-upload-vhd.md). Дополнительные требования к шифрованию дисков Azure и необходимые компоненты описаны в статье [Шифрование дисков Azure для виртуальных машин Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Инструкции по применению шифрования дисков Azure см. [в статье сценарии шифрования дисков Azure на виртуальных машинах Linux](../../linux/disk-encryption-linux.md) и связанных статьях.

## <a name="additional-information"></a>Дополнительные сведения

- При попытке предоставить виртуальную машину в подписке, которая не включена для этого предложения, вы получите следующее сообщение:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    В этом случае обратитесь в корпорацию Майкрософт или к Red Hat, чтобы включить подписку.

- Если вы изменяете моментальный снимок из образа RHEL BYOS и пытаетесь опубликовать этот пользовательский образ в [коллекции общих образов](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), необходимо предоставить сведения о плане, соответствующие исходному источнику моментального снимка. Например, команда может выглядеть следующим образом:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Обратите внимание на параметры плана в конечной строке.

    [Шифрование дисков Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) не поддерживается для пользовательских образов.

- Если вы используете автоматизацию для инициализации виртуальных машин из образов RHEL BYOS, необходимо указать параметры плана, аналогичные показанным в примерах команд. Например, если вы используете terraform, вы предоставляете сведения о плане в [блоке плана](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Дальнейшие действия

- Пошаговые руководства и сведения о программе для доступа к облаку см. в [документации по облачному доступу для Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Дополнительные сведения о инфраструктуре обновления Red Hat см. в статье [инфраструктура обновления Red Hat для Azure](./redhat-rhui.md).
- Дополнительные сведения о всех образах Red Hat в Azure см. на [странице документации](./redhat-images.md).
- Сведения о политиках поддержки Red Hat для всех версий RHEL см. на странице [жизненного цикла Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Дополнительную документацию по образам RHEL Gold см. в [документации по Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
