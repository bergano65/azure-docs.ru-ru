---
title: Руководство по очистке изолированного кластера Service Fabric — Azure Service Fabric | Документация Майкрософт
description: Из этого учебника вы узнаете, как выполнить очистку изолированного кластера
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274091"
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

## <a name="next-steps"></a>Дополнительная информация

Из четвертой части этой серии вы узнали, как очистить ресурсы, созданные на предыдущих этапах.

> [!div class="checklist"]
> * Очистка ресурсов

> [!div class="nextstepaction"]
> [В начало](service-fabric-tutorial-standalone-create-infrastructure.md)
