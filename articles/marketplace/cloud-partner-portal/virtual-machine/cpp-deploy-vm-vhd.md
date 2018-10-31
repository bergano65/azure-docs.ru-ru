---
title: Развертывание виртуальной машины из виртуальных жестких дисков в Azure Marketplace | Документация Майкрософт
description: Здесь описано, как зарегистрировать виртуальную машину из виртуального жесткого диска, развернутого в Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639378"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Развертывание виртуальной машины из дисков VHD

В этой статье описано, как зарегистрировать виртуальную машину из виртуального жесткого диска, развернутого в Azure.  Здесь перечислены необходимые средства и приведены сведения о том, как с их помощью создать пользовательский образ виртуальной машины, а затем развернуть его в Azure с использованием [портала Microsoft Azure](https://ms.portal.azure.com/) или сценариев PowerShell. 

Передав виртуальные жесткие диски (обобщенный VHD с ОС и от нуля до нескольких VHD данных) в учетную запись хранения Azure, их можно зарегистрировать как пользовательский образ виртуальной машины. Затем можно приступать к тестированию образа. Так как VHD с ОС является обобщенным, вы не можете напрямую развертывать виртуальную машину, указав URL-адрес этого VHD.

Дополнительные сведения об образах виртуальных машин см. в следующих записях блога:

- [Образ виртуальной машины](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Запись блога о способах использования PowerShell при работе с образами виртуальных машин](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Настройка необходимых средств

Если это еще не сделано, установите Azure PowerShell и Azure CLI, выполнив инструкции в следующих статьях:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="create-a-user-vm-image"></a>Создание пользовательского образа виртуальной машины

Теперь создайте неуправляемый образ на основе обобщенного VHD.

#### <a name="capture-the-vm-image"></a>Запись образа виртуальной машины

Используйте инструкции в статье о записи виртуальной машины, которая соответствует вашему подходу к получению доступа:

-  PowerShell: [Создание неуправляемого образа виртуальной машины на основе виртуальной машины Azure](../../../virtual-machines/windows/capture-image-resource.md).
-  Azure CLI: [Создание образа виртуальной машины или виртуального жесткого диска](../../../virtual-machines/linux/capture-image.md).
-  API: [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Виртуальные машины. Запись).

### <a name="generalize-the-vm-image"></a>Обобщение образа виртуальной машины

Так как вы создали пользовательский образ на основе обобщенного ранее диска VHD, он также должен быть обобщен.  Опять выберите статью, которая соответствует вашему механизму доступа.  (Возможно, вы уже обобщили диск во время записи.)

-  PowerShell: [Подготовка виртуальной машины к использованию](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm).
-  Azure CLI: [Шаг 2. Создайте образ виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image).
-  API: [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Виртуальные машины. Подготовка).


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Развертывание виртуальной машины на основе пользовательского образа VM

Теперь необходимо развернуть виртуальную машину из пользовательского образа VM с помощью портала Azure или PowerShell.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Развертывание виртуальной машины с помощью портала Azure

Разверните пользовательскую виртуальную машину на портале Azure, следуя приведенной ниже процедуре.

1.  Войдите на [портал Azure](https://portal.azure.com).

2.  Нажмите кнопку **Создать** и выполните поиск по запросу **Развертывание шаблона**, а затем выберите **Build your own template in Editor** (Создать собственный шаблон в редакторе).  <br/>
  ![Создание шаблона развертывания VHD на портале Azure](./media/publishvm_021.png)

3. Скопируйте этот [шаблон JSON](./cpp-deploy-json-template.md) и вставьте его в редактор, а затем щелкните **Сохранить**. <br/>
  ![Сохранение шаблона развертывания VHD на портале Azure](./media/publishvm_022.png)

4. Укажите значения параметров на страницах свойств **Настраиваемое развертывание**.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Параметр**              |   **Описание**                                                            |
   |  -------------              |   ---------------                                                            |
   | User Storage Account Name (Имя учетной записи хранения)   | Имя учетной записи хранения, в которой находится обобщенный виртуальный жесткий диск                    |
   | User Storage Container Name (Имя контейнера хранилища пользователя) | Имя контейнера, в котором находится обобщенный виртуальный жесткий диск                          |
   | DNS Name for Public IP (DNS-имя для общедоступного IP-адреса)      | DNS-имя общедоступного IP-адреса                                                           |
   | Admin User Name (Имя администратора)             | Имя пользователя учетной записи администратора для новой виртуальной машины                                  |
   | Пароль администратора              | Пароль учетной записи администратора для новой виртуальной машины                                  |
   | тип ОС;                     | Операционная система виртуальной машины: `Windows` \| `Linux`                                    |
   | Идентификатор подписки             | Идентификатор выбранной подписки                                      |
   | Расположение                    | Географическое расположение развертывания                                        |
   | Размер виртуальной машины                     | [Размер виртуальной машины Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), например `Standard_A2` |
   | Имя общедоступного IP-адреса      | Имя общедоступного IP-адреса                                               |
   | Имя виртуальной машины                     | Имя новой виртуальной машины                                                           |
   | имя виртуальной сети;        | Имя виртуальной сети, используемой виртуальной машиной                                   |
   | NIC Name (Имя сетевой карты)                    | Имя сетевой карты, используемой для виртуальной сети               |
   | URL-адрес VHD                     | Полный URL-адрес виртуального жесткого диска с операционной системой                                                     |
   |  |  |
            
5. Когда предоставите эти значения, нажмите кнопку **Купить**. 

Azure начнет развертывание. Будет создана виртуальная машина с указанным неуправляемым VHD в определенном пути к учетной записи хранения.  На портале Azure можно отслеживать ход выполнения, щелкнув **Виртуальные машины** в левой части.  После создания виртуальной машины состояние изменится с `Starting` на `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Развертывание виртуальной машины из PowerShell

Чтобы развернуть большую виртуальную машину из только что созданного обобщенного образа VM, используйте следующие командлеты:

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Дополнительная информация

После развертывания виртуальной машины вы будете готовы [к ее настройке](./cpp-configure-vm.md).
