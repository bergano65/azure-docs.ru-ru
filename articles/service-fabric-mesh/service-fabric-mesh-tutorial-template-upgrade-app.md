---
title: Руководство. Обновление приложения, работающего в Сетке Azure Service Fabric
description: Из этого руководства вы узнаете, как обновить приложение Service Fabric, работающее в Сетке Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 42db17fa6474d3230bc523d0cf65b375cf01276e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75351731"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Руководство. Обновление приложения Service Fabric, работающего в Сетке Service Fabric

Это руководство представляет собой первую часть цикла. Вы узнаете, как обновить приложение Service Fabric, которое было [ранее развернуто в Сетке Service Fabric](service-fabric-mesh-tutorial-template-deploy-app.md), увеличив выделенные ресурсы ЦП.  По завершении вы получите службу веб-интерфейса с большим количеством ресурсов ЦП.

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * изменение конфигураций приложения;
> * обновление приложения, работающего в Сетке Service Fabric;

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [развертывание приложения в Сетке Service Fabric с помощью шаблона](service-fabric-mesh-tutorial-template-deploy-app.md);
> * [масштабирование приложения, работающего в Сетке Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md);
> * обновление приложения, работающего в Сетке Service Fabric;
> * [Удаление приложения](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет Azure подписки до начала работы, можно [создать бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Откройте [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) или [установите Azure CLI и интерфейс командной строки Сетки Azure Service Fabric на локальный компьютер](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Обновление конфигураций приложения

Одним из основных преимуществ развертывания приложений в Сетке Service Fabric является возможность легкого обновления конфигураций приложения.  Например, ресурсов ЦП или памяти для служб.

В этом руководстве используется пример To Do List, который был [развернут ранее](service-fabric-mesh-tutorial-template-deploy-app.md) и должен быть запущен. Приложение содержит две службы: WebFrontEnd и ToDoService. Каждая служба изначально была развернута с использованием 0,5 ресурсов ЦП.  Чтобы просмотреть ресурсы ЦП службы WebFrontEnd, используйте следующую команду:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

В шаблоне развертывания ресурса приложения для каждой службы определено свойство *cpu*, с помощью которого можно установить запрашиваемые ресурсы ЦП. Приложение может состоять из нескольких служб с уникальными параметрами *cpu*, развертывание и управление которыми осуществляется совместно. Чтобы увеличить ресурсы ЦП службы веб-интерфейса, измените значение *cpue* в шаблоне развертывания или файле параметров.  Затем обновите приложение.

### <a name="modify-the-deployment-template-parameters"></a>Изменение параметров шаблона развертывания

Если в шаблоне есть значения, которые изменятся после развертывания приложения или которые вы хотели бы изменять для разных развертываний (если планируете повторно использовать этот шаблон), рекомендуется параметризировать эти значения.

Ранее приложение было развернуто с помощью файла [шаблона развертывания mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) и файла [параметров mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Откройте файл параметров [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) локально и задайте для *frontEndCpu* значение 1.

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Сохраните изменения в файле параметров.  

Параметры *frontEndCpu* объявлены в разделе *parameters*[шаблона развертывания mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json).

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

Свойство службы WebFrontEnd *codePackages -> resources -> requests-> cpu* ссылается на параметр *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Обновление приложения.

Когда приложение запущено, его можно обновить, повторно развернув шаблон и обновленный файл параметров.

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Будет запущено последовательное обновление приложения, и через несколько минут вы увидите, что ресурсов ЦП стало больше.  Чтобы просмотреть ресурсы ЦП службы WebFrontEnd, используйте следующую команду:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * изменение конфигураций приложения;
> * обновление приложения, работающего в Сетке Service Fabric;

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Удаление приложения Сетки Service Fabric](service-fabric-mesh-tutorial-template-remove-app.md)
