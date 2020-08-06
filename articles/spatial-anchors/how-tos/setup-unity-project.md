---
title: Установка пространственных привязок Azure для Unity
description: Настройка проекта Unity для использования пространственных привязок Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812337"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Настройка пространственных привязок Azure в проекте Unity

## <a name="requirements"></a>Требования

Пространственные привязки Azure в настоящее время поддерживают Unity 2019,4 (LTS) со следующими конфигурациями.

* Unity 2019,4 с AR Foundation 3,1 поддерживается в пространственных привязках Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Настройка проекта

Пространственные привязки Azure для Unity в настоящее время распространяются с помощью пакета ресурсов Unity ( `.unitypackage` ), который можно найти в [выпусках GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Импорт пакета ресурсов

1. Скачайте `AzureSpatialAnchors.unitypackage` файл для версии, которую вы хотите ориентировать, из [выпусков GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Следуйте приведенным [здесь](https://docs.unity3d.com/Manual/AssetPackagesImport.html) инструкциям, чтобы импортировать пакет ресурсов Unity в проект.

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Как создавать и размещать привязки в Unity](./create-locate-anchors-unity.md)
