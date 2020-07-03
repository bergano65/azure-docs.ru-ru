---
title: Очистка изолированного кластера
description: Из этого руководства вы узнаете, как очистить ресурсы AWS и Azure в изолированном кластере Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639026"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Руководство по очистке изолированного кластера

Изолированные кластеры Service Fabric предоставляют возможность выбрать собственную среду и создать кластер в рамках подхода "любая ОС, любое облако", который применяется на платформе Service Fabric. Из этой серии учебников вы узнаете, как создать изолированный кластер, размещенный в AWS или Azure, и установить в нем приложение.

Это руководство представляет собой четвертую часть цикла. В этой части учебника показано, как очистить ресурсы AWS или Azure, созданные для размещения кластера Service Fabric.

В четвертой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Очистка кластера Service Fabric
> * Очистка ресурсов AWS или Azure

## <a name="clean-up-service-fabric-cluster"></a>Очистка кластера Service Fabric

1. Подключитесь по протоколу RDP к виртуальной машине, использованной для установки Service Fabric.
2. Откройте PowerShell.
3. Перейдите в каталог извлеченной папки из второго руководства.
4. Чтобы удалить кластер Service Fabric, выполните следующую команду:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. При появлении запроса нажмите `Y`. Если операция выполнена успешно, выходные данные будут выглядеть так, как показано ниже, но вместо приведенных здесь примеров будут указаны ваши IP-адреса.

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>Очистка ресурсов AWS

1. Войдите в учетную запись AWS.
2. Перейдите к консоли EC2.
3. Выберите три узла, созданные в первой части этой серии руководств.
4. Щелкните **Действия** > **Состояние экземпляра** > **Завершить**.

## <a name="clean-up-azure-resources"></a>Очистка ресурсов Azure

1. Войдите на портал Azure.
2. Перейдите в раздел **Виртуальные машины**.
3. Установите флажки для трех узлов, созданных в первой части этой серии учебников.
4. Нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Из четвертой части этой серии вы узнали, как очистить ресурсы, созданные на предыдущих этапах.

> [!div class="checklist"]
> * Очистка ресурсов

> [!div class="nextstepaction"]
> [В начало](service-fabric-tutorial-standalone-create-infrastructure.md)
