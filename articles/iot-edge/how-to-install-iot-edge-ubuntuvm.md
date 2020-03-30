---
title: Запуск Azure IoT Edge на виртуальных машинах Ubuntu | Документация Майкрософт
description: Инструкции по настройке Azure IoT Edge для виртуальных машин Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349597"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Запуск Azure IoT Edge на виртуальных машинах Ubuntu

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака.

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены шаги по развертыванию виртуальной машины Ubuntu 18.04 LTS с установленным и настроенным временем выполнения Azure IoT Edge с помощью предварительно поставленной строки соединения устройства. Развертывание осуществляется с помощью [шаблона Azure Resource Manager,](../azure-resource-manager/templates/overview.md) основанного на [облачном ресурсе,](../virtual-machines/linux/using-cloud-init.md
) который поддерживается в репозитории проектов [iotedge-vm.](https://github.com/Azure/iotedge-vm-deploy)

При первом загрузке виртуальная машина Ubuntu 18.04 LTS [устанавливает последнюю версию времени выполнения Azure IoT Edge с помощью cloud-init.](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt) Он также устанавливает поставляемую строку соединения перед запуском времени, что позволяет легко настроить и подключить устройство IoT Edge без необходимости запуска SSH или удаленного сеанса рабочего стола. 

## <a name="deploy-using-deploy-to-azure-button"></a>Развертывание с помощью развертывания в кнопку Azure

Кнопка [развертывания в Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) позволяет оптимистично развертывать [шаблоны управления ресурсами Azure,](../azure-resource-manager/templates/overview.md) поддерживаемые на GitHub.  В этом разделе будет продемонстрирована использование кнопки Deploy to Azure, содержащейся в репозитории проектов [iotedge-vm.](https://github.com/Azure/iotedge-vm-deploy)  


1. Мы разместим с помощью шаблона управления ресурсами Azure IoT Edge с поддержкой Linux VM с помощью шаблона управления ресурсами Azure.  Для начала нажмите кнопку ниже:

    [![Развертывание в кнопку Azure для развертывания iotedge-vm](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. На недавно запущенном окне заполните доступные поля формы:

    > [!div class="mx-imgBorder"]
    > [![Скриншот, показывающий шаблон iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Подписка**: Активная подписка Azure для развертывания виртуальной машины.

    **Группа ресурсов**: Существующая или недавно созданная группа ресурсов, содержащая виртуальную машину и связанные с ней ресурсы.

    **Префикс DNS Label:** требуемая ценность по вашему выбору, которая используется для префикса хоста виртуальной машины.

    **Имя пользователя admin**: Имя пользователя, которому будут предоставлены корневые привилегии при развертывании.

    **Строка подключения устройств**: [строка подключения к устройству](how-to-register-device.md) для устройства, созданного в пределах предполагаемого [концентратора IoT.](../iot-hub/about-iot-hub.md)

    **Размер VM**: [размер](../cloud-services/cloud-services-sizes-specs.md) виртуальной машины, которая будет развернута

    **Ubuntu OS Version**: Версия ОС Ubuntu будет установлена на базе виртуальной машины.

    **Местоположение**: [Географический регион](https://azure.microsoft.com/global-infrastructure/locations/) для развертывания виртуальной машины в, это значение по умолчанию попадает в расположение выбранной группы ресурсов.

    **Тип аутентификации**: Выберите **sshPublicKey** или **пароль** в зависимости от ваших предпочтений.

    **Пароль или ключ от админа:** значение публичного ключа SSH или значение пароля в зависимости от выбора типа аутентификации.

    Когда все поля заполнены, выберите флажок в нижней части страницы, чтобы принять условия, и выберите **Покупка,** чтобы начать развертывание.

1. Убедитесь, что развертывание завершено успешно.  Виртуальный ресурс машины должен был быть развернут в выбранной группе ресурсов.  Принять к сведению название машины, `vm-0000000000000`это должно быть в формате . Также обратите внимание на связанное **DNS Name**, `<dnsLabelPrefix>`которое должно быть в формате. `<location>`.cloudapp.azure.com.

    **Имя DNS** можно получить в разделе **Обзор** недавно развернутой виртуальной машины на портале Azure.

    > [!div class="mx-imgBorder"]
    > [![Скриншот, показывающий dns имя iotedge Vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Если вы хотите внести SSH в этот VM после настройки, используйте связанное **имя DNS** с командой:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Развертывание с помощью Azure CLI

1. Убедитесь, что вы установили расширение Azure CLI iot с:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Далее, если вы используете Azure CLI на рабочем столе, начните с входа в систему:

   ```azurecli-interactive
   az login
   ```

1. Если у вас несколько подписок, выберите подписку, которая вы хотите использовать:
   1. Отобразите список подписок:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Копирование поля SubscriptionID для подписки, которая вы хотите использовать.

   1. Установите рабочую подписку с помощью идентификатора, который вы скопировали:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Создайте группу ресурсов (или выберите уже имеющуюся на следующих этапах):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Создайте виртуальную машину:

    Для использования **аутентификацииТип** `password`, см.

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Чтобы проверить подлинность с помощью ключа SSH, вы можете `sshPublicKey`сделать это, указав **аутентификациюType,** а затем укажите значение ключа SSH в параметре **adminPasswordOrKey.**  Ниже приведен пример такого файла.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Убедитесь, что развертывание завершено успешно.  Виртуальный ресурс машины должен был быть развернут в выбранной группе ресурсов.  Принять к сведению название машины, `vm-0000000000000`это должно быть в формате . Также обратите внимание на связанное **DNS Name**, `<dnsLabelPrefix>`которое должно быть в формате. `<location>`.cloudapp.azure.com.

    **Имя DNS** может быть получено из вывода jSON-формата предыдущего шага, в разделе **выходов** как часть публичной записи **SSH.**  Значение этой записи может быть использовано для SSH в недавно развернутой машине.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **Имя DNS** также можно получить в разделе **Обзор** недавно развернутой виртуальной машины на портале Azure.

    > [!div class="mx-imgBorder"]
    > [![Скриншот, показывающий dns имя iotedge Vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Если вы хотите внести SSH в этот VM после настройки, используйте связанное **имя DNS** с командой:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если у вас возникли проблемы с ioT Edge время выполнения установки правильно, проверить страницу [устранения неполадок.](troubleshoot.md)

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

Если вы хотите открыть порты для доступа к VM через SSH или другие входящие соединения, обратитесь к документации Azure Virtual Machines об [открытии портов и конечных точек Linux VM](../virtual-machines/linux/nsg-quickstart.md)
