---
title: Red Hat Enterprise Linux образы с собственными подпискуами | Документация Майкрософт
description: Сведения о собственных образах подписки для Red Hat Enterprise Linux в Azure
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
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486512"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux собственные образы подписок в Azure
Образы Red Hat Enterprise Linux (RHEL) доступны в Azure через модель с оплатой по мере использования (PAYG) или с помощью собственной подписки (BYOS). В этом документе представлен обзор образов BYOS в Azure.

## <a name="important-points-to-consider"></a>Важные моменты, которые следует учитывать

- Образы RHEL BYOS, представленные в этой программе, являются готовыми RHEL образами, аналогичными изображениям RHEL PAYG в коллекции Azure или Marketplace. Процесс регистрации для получения образов находится на этапе предварительной версии.

- Образы соответствуют текущим политикам, описанным в разделе [образы Red Hat Enterprise Linux в Azure](./redhat-images.md) .

- Политики поддержки уровня "Стандартный" применяются к виртуальным машинам, созданным из этих образов

- Виртуальные машины, подготовленные из образов RHEL BYOS, не несут RHEL плату, связанную с образами RHEL PAYG

- Образы не имеют прав, поэтому необходимо использовать диспетчер подписки для регистрации и подписки на виртуальные машины, чтобы получать обновления с Red Hat напрямую.

- Сейчас невозможно динамически переключаться между моделями выставления счетов BYOS и PAYG для образов Linux. Для переключения модели выставления счетов требуется повторное развертывание виртуальной машины из соответствующего образа

- Шифрование дисков Azure (ADE) поддерживается в этих образах RHEL BYOS. Сейчас поддержка ADE доступна в предварительной версии. Перед настройкой ADE необходимо зарегистрироваться в Red Hat с помощью диспетчера подписки. После регистрации для настройки ADE см. статью [Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview) .

- Хотя изображения не изменятся (за пределами стандартных обновлений и исправлений), процесс регистрации находится на этапе предварительной версии, и последовательность будет улучшена для упрощения процесса.

- Вы полностью контролируете виртуальные машины, которые уже подготовлены из этих образов или моментальных снимков, независимо от окончательной реализации.

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Требования и условия для доступа к образам RHEL BYOS

1. Знакомство с терминами [программы "Облачный доступ" Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) и регистрация на [странице регистрации в облаке доступа Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Заполните [форму запроса RHEL BYOS Access](https://aka.ms/rhel-byos). Вам потребуется использовать номер учетной записи Red Hat, а также подписки Azure, с которыми вы будете получать доступ к образам RHEL BYOS с помощью.

1. При проверке и утверждении как с помощью Red Hat, так и с Майкрософт, ваши подписки Azure будут доступны для доступа к изображениям.

### <a name="expected-time-for-image-access"></a>Ожидаемое время доступа к изображению

После завершения формы RHEL BYOS и принятия условий Red Hat проверит допустимость образов BYOS в течение трех рабочих дней и, если это допустимо, вы будете получать доступ к изображениям BYOS в течение одного рабочего дня.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Использование образов RHEL BYOS на портале Azure

1. После включения подписки для образов RHEL BYOS можно найти ее в [портал Azure](https://portal.azure.com) , перейдя к разделу **Создание ресурса** , а затем **Просмотреть все**.

1. В верхней части страницы вы увидите, что у вас есть частные предложения.

    ![Частные предложения Marketplace](./media/rhel-byos-privateoffers.png)

1. Вы можете щелкнуть фиолетовой ссылкой или прокрутить вниз до нижней части страницы, чтобы увидеть ваши частные предложения.

1. Остальная часть подготовки в пользовательском интерфейсе не будет отличаться от других существующих изображений Red Hat. Выберите версию RHEL и следуйте инструкциям по подготовке виртуальной машины. Этот процесс также позволит принять условия образа на заключительном этапе.

>[!NOTE]
>Эти действия не позволяют использовать образ RHEL BYOS для программного развертывания — потребуется дополнительный шаг, как описано в разделе "Дополнительные сведения" ниже.

Остальная часть этого документа посвящена методу CLI для предоставления и принятия условий на изображении. Пользовательский интерфейс и CLI полностью взаимозаменяемы, как и окончательный результат (подготовленная виртуальная машина RHEL BYOS).

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Используйте образы RHEL BYOS из Azure CLI
Приведенный ниже набор инструкций поможет вам выполнить начальную процедуру развертывания виртуальной машины RHEL с помощью Azure CLI. В этих инструкциях предполагается, что [Azure CLI установлен](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Обязательно используйте все строчные буквы на издателе, предложения, планы и ссылки на изображения для всех следующих команд.

1. Убедитесь, что вы используете нужную подписку:
    ```azurecli
    az account show -o=json
    ```

1. Создайте группу ресурсов для виртуальной машины RHEL BYOS:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Примите условия использования образа:
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

1. Подготавливает виртуальную машину, выполнив ту же команду, которая указана выше, без аргумента `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Подключитесь к виртуальной машине по протоколу SSH и убедитесь в отсутствии образа. ДЛЯ этого запустите `sudo yum repolist`. В выходных данных будет предложено использовать диспетчер подписки для регистрации виртуальной машины с помощью Red Hat.

## <a name="additional-information"></a>Дополнительные сведения
- При попытке подготовки виртуальной машины в подписке, для которой не включено это предложение, появится следующее сообщение об ошибке. чтобы включить подписку, обратитесь в корпорацию Майкрософт или Red Hat.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- При создании моментального снимка из образа RHEL BYOS и публикации образа в [коллекции Shared Images](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)необходимо предоставить сведения о плане, соответствующие исходному источнику моментального снимка. Например, команда может выглядеть следующим образом (Обратите внимание на параметры плана в конечной строке):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Если вы используете службу автоматизации для инициализации виртуальных машин из образов RHEL BYOS, необходимо указать параметры плана, аналогичные показанным выше. Например, если вы используете terraform, сведения о плане можно указать в [блоке плана](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [инфраструктуре обновления Azure Red Hat](./redhat-rhui.md).
* Дополнительные сведения об образах Red Hat в Azure можно найти на [странице документации](./redhat-images.md).
* Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).