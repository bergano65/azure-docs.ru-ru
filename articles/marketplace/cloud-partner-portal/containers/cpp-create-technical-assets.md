---
title: Создание образа Azure контейнеры технических средств | Azure Marketplace
description: Создание технических ресурсов для контейнера Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794345"
---
# <a name="prepare-your-container-technical-assets"></a>Подготовка технических ресурсов для контейнера

В этой статье приведены инструкции по настройке предложения контейнера в Azure Marketplace и описаны требования к ней.

## <a name="before-you-begin"></a>Перед началом работы

Ознакомьтесь с документацией по [Экземплярам контейнеров Azure](https://docs.microsoft.com/azure/container-instances), включающей руководства, примеры и другие материалы.

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, сборка и тестирование этих ресурсов занимают время и требуют технических знаний и платформы Azure, и технологий, используемых для создания предложения.
 
Помимо знаний о предметной области решения команда разработчиков также должна располагать знаниями о технологиях Майкрософт, в частности у них должно быть:

-   базовое представление о [службах Azure](https://azure.microsoft.com/services/); 
-   умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
-   опыт работы с [виртуальными машинами Azure](https://azure.microsoft.com/services/virtual-machines/), [службой хранилища Azure](https://azure.microsoft.com/services/?filter=storage) и [сетями Azure](https://azure.microsoft.com/services/?filter=networking);
-   опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
-   опыт работы с [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Выберите одну или обе следующие среды сценариев, которые понадобятся вам при управлении образом контейнера:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure)

Кроме того, мы рекомендуем добавить следующие инструменты в среду разработки:

-   [Azure Storage Explorer;](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Добавочный номер: [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Мы также предлагаем проверить доступные средства на странице [Средства для разработчиков Azure](https://azure.microsoft.com/tools/) и, если вы используете Visual Studio, на странице [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

Дополнительную информацию см. в следующих статьях:

* [Учебник. Создать образ контейнера для развертывания экземпляры контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Учебник. Создание и развертывание образов контейнеров в облаке с реестром контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Дальнейшие действия

[Создание предложения контейнера](./cpp-create-offer.md)
