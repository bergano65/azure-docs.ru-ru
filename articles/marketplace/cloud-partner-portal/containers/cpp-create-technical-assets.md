---
title: Создание технических ресурсов для образов контейнеров Azure | Документация Майкрософт
description: Создание технических ресурсов для контейнера Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 5a7531be73a872d9c088a0bf02a8686f947c220a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047370"
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
    *   Расширение: [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Мы также предлагаем проверить доступные средства на странице [Средства для разработчиков Azure](https://azure.microsoft.com/tools/) и, если вы используете Visual Studio, на странице [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

- Создайте и настройте виртуальный жесткий диск (VHD) для виртуальной машины контейнера. Этот виртуальный жесткий диск содержит операционную систему (Windows, Linux или Ubuntu) для контейнера. Могут потребоваться дополнительные диски данных.
- Настройте ОС виртуальной машины, размер виртуальной машины, открываемые порты и любые присоединенные диски данных.
- Установите приложение и другое программное обеспечение, необходимое для вашего предложения. Например, программное обеспечение базы данных, программное обеспечение сторонних производителей или пользовательское приложение.

## <a name="next-steps"></a>Дальнейшие действия

[Создание предложения контейнера](./cpp-create-offer.md)
