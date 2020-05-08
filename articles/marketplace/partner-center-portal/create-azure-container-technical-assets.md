---
title: Технические понятия о предложениях контейнеров Azure. коммерческий рынок Майкрософт
description: Технические ресурсы и рекомендации, помогающие настроить предложение контейнера в Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: b3c6f88df151cc497f0de670d5d78a05c7477459
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791890"
---
# <a name="create-an-azure-container-offer"></a>Создание предложения для контейнера Azure

> [!IMPORTANT]
> Мы переносим Управление предложениями контейнеров Azure из Портал Cloud Partner в центр партнеров. Пока ваши предложения не будут перенесены, следуйте инструкциям в статье [Подготовка технических ресурсов контейнера](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) для портал Cloud Partner для управления вашими предложениями.

В этой статье приводятся технические ресурсы и рекомендации, которые помогут вам создать предложение контейнера в Azure Marketplace.

## <a name="before-you-begin"></a>Перед началом

Краткие руководства, учебники и примеры см. в [документации по службе "экземпляры контейнеров Azure](https://docs.microsoft.com/azure/container-instances)".

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, сборка и тестирование этих ресурсов занимает время и требует технических знаний о платформе Azure и технологиях, используемых для создания предложения.

Помимо домена решения, группа разработчиков должна иметь знания о следующих технологиях Майкрософт:

- базовое представление о [службах Azure](https://azure.microsoft.com/services/);
- умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
- Опыт работы с [виртуальными машинами Azure](https://azure.microsoft.com/services/virtual-machines/), службой [хранилища Azure](https://azure.microsoft.com/services/?filter=storage)и [сетью Azure](https://azure.microsoft.com/services/?filter=networking)
- опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
- Опыт работы с [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Выберите одну или обе следующие среды сценариев для управления образом контейнера:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Мы рекомендуем добавить эти средства в среду разработки:

- [Обозреватель службы хранилища Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Расширение: [Инструменты Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
  - Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify).
  - Расширение: [ПЕРЕРАБОТАНЫ JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Ознакомьтесь с доступными инструментами на странице [средства для разработчиков Azure](https://azure.microsoft.com/) . Если вы используете Visual Studio, ознакомьтесь со средствами, доступными в [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

Дополнительные сведения см. в следующих учебниках:

- [Руководство. Создание образа контейнера для развертывания в службе "экземпляры контейнеров Azure"](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Руководство. Создание и развертывание образов контейнеров в облаке с помощью задач реестра контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Дальнейшие действия

- [Создайте предложение контейнера](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
