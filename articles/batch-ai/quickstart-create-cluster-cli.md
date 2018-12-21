---
title: Краткое руководство по Azure — создание кластера Batch AI с помощью Azure CLI | Документация Майкрософт
description: Краткое руководство — создание кластера Batch AI для обучения моделей машинного обучения и искусственного интеллекта с помощью Azure CLI
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 1ea12c9a544704ea91b85ae944e611e6769b5592
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407139"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Краткое руководство. Создание кластера Batch AI для заданий обучения с помощью Azure CLI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

В этом кратком руководстве показано, как с помощью Azure CLI создать кластер Batch AI, которые можно использовать для обучения моделей искусственного интеллекта и машинного обучения. Управляемая служба Batch AI позволяет специалистам по обработке и анализу данных и исследователям искусственного интеллекта обучать модели искусственного интеллекта и машинного обучения на кластерах виртуальных машин Azure в большом масштабе.

Изначально в кластере есть один узел GPU. Выполнив инструкции из этого краткого руководства, вы получите масштабируемый кластер для обучения моделей. Задания обучения в этот кластер можно отправлять через Batch AI, средства [машинного обучения Azure](../machine-learning/service/overview-what-is-azure-ml.md) или расширение [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI на локальном компьютере, для выполнения инструкций из этого руководства вам потребуется Azure CLI версии 2.0.38 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

В этом кратком руководстве предполагается, что вы выполняете команды в оболочке Bash через Cloud Shell или на локальном компьютере.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды `az group create`. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus2*. Обязательно выберите расположение, в котором доступна служба Batch AI, например "Восточная часть США 2".

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Создание кластера искусственного интеллекта пакетной службы

Сначала создайте `az batchai workspace create`рабочую область*Batch AI с помощью команды*. Рабочая область нужна для того, чтобы упорядочить кластеры Batch AI и другие ресурсы.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Чтобы создать кластер Batch AI, используйте команду `az batchai cluster create`. Следующий пример создает кластер со следующими свойствами:

* содержит один узел с размером виртуальной машины NC6 с одним GPU типа NVIDIA Tesla K80; 
* выполняет стандартный образ Ubuntu Server, предназначенный для размещения контейнерных приложений. Он хорошо подходит для большинства рабочих нагрузок обучения; 
* добавлена учетная запись пользователя с именем *myusername* и созданы ключи SSH, если они не существовали ранее в расположении ключей по умолчанию (*~/.ssh*) в локальной среде. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Выходные данные команды отображают свойства кластера. Создание и запуск узла занимают несколько минут. Чтобы просмотреть состояние кластера, запустите команду `az batchai cluster show`. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

На раннем этапе создания кластера она возвращает примерно такой результат, где кластер имеет состояние `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
Кластер будет готов к работе, когда его состояние получит значение `steady`, а состояние единственного узла — `Idle`.

## <a name="list-cluster-nodes"></a>Список узлов кластера 

Если вам нужно подключиться к узлам кластера (в данном случае к одному узлу) для установки приложений или выполнения обслуживания, получите параметры подключения с помощью команды `az batchai cluster node list`:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

Выходные данные возвращаются в формате JSON:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Используйте эту информацию для создания SSH-подключения к узлу. Например, подставьте правильное значение IP-адреса в следующую команду:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Выйдите из сеанса SSH, чтобы продолжить работу.

## <a name="resize-the-cluster"></a>Изменение размера кластера

При использовании кластера для задания обучения вам могут потребоваться дополнительные ресурсы. Например, чтобы увеличить размер до 2 узлов для задания распределенного обучения, выполните команду [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Изменение размера кластера может занять несколько минут.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить изучение примеров и руководств по Batch AI, сохраните созданную в этом руководстве рабочую область Batch AI. 

Плата за кластер Batch AI насчитывается только во время работы узлов. Если вы хотите сохранить конфигурацию кластера, пока нет новых заданий для выполнения в нем, уменьшите размер кластера до 0 узлов. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Позже вы сможете увеличить масштаб до одного или нескольких узлов, чтобы выполнить следующее задание. Когда кластер станет не нужен, удалите его с помощью команды `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

Когда группа ресурсов Batch AI станет ненужной, ее можно удалить с помощью команды `az group delete`. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать кластер Batch AI с помощью Azure CLI. Дополнительные сведения об использовании кластера Batch AI для обучения модели вы найдете в кратком руководстве по обучению модели глубокого обучения.

> [!div class="nextstepaction"]
> [Обучение модели глубокого обучения](./quickstart-tensorflow-training-cli.md)
