---
title: Краткое руководство. Создание масштабируемого набора виртуальных машин Linux с помощью шаблона Azure Resource Manager
description: Узнайте, как быстро создать масштабируемый набор виртуальных машин Linux с помощью шаблона Azure Resource Manager, который позволяет развернуть пример приложения и настроить правила автоматического масштабирования.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 4c0bac943be996c02436824334bd79a270f9a2e2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010466"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-resource-manager-template"></a>Краткое руководство. Создание масштабируемого набора виртуальных машин Linux с помощью шаблона Azure Resource Manager

Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования в зависимости от использования ЦП, объема памяти или сетевого трафика. После этого Azure Load Balancer будет распределять трафик между экземплярами виртуальных машин в масштабируемом наборе. С помощью этого краткого руководства вы создадите масштабируемый набор виртуальных машин и развернете пример приложения с помощью шаблона Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Нет.

## <a name="create-a-scale-set"></a>Создание масштабируемого набора

Шаблоны Azure Resource Manager позволяют развертывать группы связанных ресурсов. С помощью одного шаблона можно создать масштабируемый набор виртуальных машин, установить приложения и настроить правила автоматического масштабирования. Вы можете повторно использовать этот шаблон, применив переменные и параметры, чтобы обновить существующие или создать дополнительные масштабируемые наборы. Шаблоны можно развернуть с помощью портала Azure, Azure CLI или Azure PowerShell либо же на основе конвейеров непрерывной интеграции и непрерывной поставки (CI/CD).

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-bottle-autoscale/azuredeploy.json" range="1-330" highlight="176-264":::

В шаблоне определены следующие ресурсы:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers);
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets);
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings).

#### <a name="define-a-scale-set"></a>Определение масштабируемого набора

Выделенная часть — это определение ресурса масштабируемого набора. Чтобы создать масштабируемый набор с помощью шаблона, определите необходимые ресурсы. Ниже перечислены основные элементы и типы ресурсов для масштабируемого набора виртуальных машин.

| Свойство                     | Описание свойства                                  | Пример значения в шаблоне                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Создаваемый тип ресурса Azure                            | Microsoft.Compute/virtualMachineScaleSets; |
| name                         | Имя масштабируемого набора                                       | myScaleSet                                |
| location                     | Расположение для создания масштабируемого набора                     | Восточная часть США                                   |
| sku.name                     | Размер виртуальной машины для каждого экземпляра в масштабируемом наборе                  | Standard_A1                               |
| sku.capacity                 | Количество экземпляров виртуальных машин, которое требуется изначально создать           | 2                                         |
| upgradePolicy.mode           | Режим обновления экземпляра виртуальной машины при внесении изменений              | Автоматически                                 |
| imageReference               | Платформа или пользовательский образ, используемые для экземпляров виртуальных машин | Сервер Canonical Ubuntu 16.04-LTS         |
| osProfile.computerNamePrefix | Префикс имени для каждого экземпляра виртуальной машины                     | myvmss                                    |
| osProfile.adminUsername      | Имя пользователя для каждого экземпляра виртуальной машины                        | azureuser                                 |
| osProfile.adminPassword      | Пароль для каждого экземпляра виртуальной машины                        | P@ssw0rd!                                 |

Чтобы настроить шаблон масштабируемого набора, вы можете изменить размер виртуальной машины или начальную емкость. Еще один вариант — использовать другую платформу или пользовательский образ.

#### <a name="add-a-sample-application"></a>Добавление примера приложения

Для проверки масштабируемого набора установите базовое веб-приложение. Когда вы развертываете масштабируемый набор, расширения виртуальных машин могут предусматривать задачи автоматизации и настройки после развертывания, например задачи установки приложения. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Чтобы применить расширение к масштабируемому набору, добавьте раздел *extensionProfile* в предыдущий пример ресурса. Как правило, профиль расширения определяет следующие свойства:

- тип расширения;
- издатель расширения;
- версия расширения;
- расположение скриптов настройки или установки;
- команды для выполнения на экземплярах виртуальных машин.

В шаблоне используется настраиваемое расширение скриптов для установки [Bottle](https://bottlepy.org/docs/dev/), веб-платформы Python и простого HTTP-сервера.

Два скрипта определены в файлах **fileUris** - *installserver.sh* и *workserver.py*. Эти файлы скачиваются из GitHub, затем *commandToExecute* выполняет `bash installserver.sh` для установки и настройки приложения.

### <a name="deploy-the-template"></a>Развертывание шаблона

Вы можете развернуть шаблон, нажав следующую кнопку **Развертывание в Azure**. После ее нажатия откроется портал Azure, загрузится весь шаблон и появится диалог для ввода параметров, таких как имя масштабируемого набора, число экземпляров и учетные данные администратора.

[![Развертывание шаблона в Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Вы также можете развернуть шаблон Resource Manager с помощью Azure CLI:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

В ответ на запросы предоставьте имя масштабируемого набора, число экземпляров и учетные данные администратора для экземпляров виртуальных машин. Создание масштабируемого набора и вспомогательных ресурсов занимает несколько минут.

## <a name="test-the-deployment"></a>тестирование развертывания

Чтобы увидеть, как работает масштабируемый набор, откройте пример веб-приложения в браузере. Получите общедоступный IP-адрес подсистемы балансировки нагрузки с помощью команды [az network public-ip list](/cli/azure/network/public-ip):

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Введите в браузер общедоступный IP-адрес своей подсистемы балансировки нагрузки в формате *http:\//publicIpAddress:9000/do_work*. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Веб-страница NGINX по умолчанию](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов, масштабируемый набор и все связанные ресурсы с помощью команды [az group delete](/cli/azure/group), как показано ниже. При использовании параметра `--no-wait` управление возвращается в командную строку без ожидания завершения операции. Параметр `--yes` подтверждает, что вы хотите удалить ресурсы без дополнительного запроса.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы создали масштабируемый набор Linux с помощью шаблона Azure и применили расширение пользовательского скрипта, чтобы установить базовый веб-сервер NGINX на экземплярах виртуальной машины. Чтобы получить дополнительные сведения, продолжите роботу с руководством по созданию масштабируемых наборов виртуальных машин Azure и управлению ими.

> [!div class="nextstepaction"]
> [Создание масштабируемых наборов виртуальных машин Azure и управление ими](tutorial-create-and-manage-cli.md)
