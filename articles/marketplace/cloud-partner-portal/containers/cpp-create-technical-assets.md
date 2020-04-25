---
title: Создание ресурсов Azure с изображениями в виде контейнеров | Azure Marketplace
description: Создание технических ресурсов для контейнера Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 68db606c9a01c4b1122f9b0cce620762485ca40a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148274"
---
# <a name="prepare-your-container-technical-assets"></a>Подготовка технических ресурсов для контейнера

> [!IMPORTANT]
> Начиная с 13 апреля 2020 г. Мы начинаем перемещаться по управлению предложениями контейнеров Azure в центр партнеров. После миграции вы создадите предложения в центре партнеров и будете управлять ими. Следуйте инструкциям в статье [Подготовка технических ресурсов контейнеров Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) для управления перенесенными предложениями.

В этой статье приведены инструкции по настройке предложения контейнера в Azure Marketplace и описаны требования к ней.

## <a name="before-you-begin"></a>Подготовка к работе

Ознакомьтесь с документацией по [Экземплярам контейнеров Azure](https://docs.microsoft.com/azure/container-instances), включающей руководства, примеры и другие материалы.

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, сборка и тестирование этих ресурсов занимают время и требуют технических знаний и платформы Azure, и технологий, используемых для создания предложения.
 
Помимо знаний о предметной области решения команда разработчиков также должна располагать знаниями о технологиях Майкрософт, в частности у них должно быть:

-    базовое представление о [службах Azure](https://azure.microsoft.com/services/); 
-    умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
-    опыт работы с [виртуальными машинами Azure](https://azure.microsoft.com/services/virtual-machines/), [службой хранилища Azure](https://azure.microsoft.com/services/?filter=storage) и [сетями Azure](https://azure.microsoft.com/services/?filter=networking);
-    опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
-    опыт работы с [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Выберите одну или обе следующие среды сценариев, которые понадобятся вам при управлении образом контейнера:

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Кроме того, мы рекомендуем добавить следующие инструменты в среду разработки:

-    [Обозреватель службы хранилища Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Расширение: [Инструменты Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
    *    Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify).
    *    Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Мы также предлагаем проверить доступные средства на странице [Средства для разработчиков Azure](https://azure.microsoft.com/tools/) и, если вы используете Visual Studio, на странице [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

Дополнительную информацию см. в следующих статьях:

* [Руководство. Создание образа контейнера для развертывания в службе "экземпляры контейнеров Azure"](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Руководство. Создание и развертывание образов контейнеров в облаке с помощью задач реестра контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Дальнейшие шаги

[Создание предложения контейнера](./cpp-create-offer.md)
