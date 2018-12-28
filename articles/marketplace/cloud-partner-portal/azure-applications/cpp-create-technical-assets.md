---
title: Создание технических ресурсов для приложения Azure | Документация Майкрософт
description: Создание технических ресурсов для предложения приложения Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6050ad98c87dbe38516a6ee3c4862495ad868031
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414354"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Подготовка технических ресурсов для приложения Azure

В этой статье описываются средства подготовки технических ресурсов для предложения приложения Azure.

## <a name="before-you-begin"></a>Перед началом работы

Ознакомьтесь с документацией по приложениям Azure, включающей руководства, примеры и другие материалы.

- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Краткие руководства:

  - [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/)
  - [Шаблоны быстрого запуска Azure в GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Публикация определения приложения](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Развертывание приложения из каталога служб](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Руководства:

  - [Создание файлов определений](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Публикация приложения из Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

 - Примеры:

   - [Интерфейс командной строки Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
   - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
   - [Решения для управляемых приложений](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, сборка и тестирование этих ресурсов занимают время и требуют технических знаний и платформы Azure, и технологий, используемых для создания предложения.

Ваша команда разработчиков должна иметь опыт работы с технологиями Майкрософт, в частности:

- базовое представление о [службах Azure](https://azure.microsoft.com/services/);
- умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
- с [виртуальными машинами Azure](https://azure.microsoft.com/services/virtual-machines/), [службой хранилища Azure](https://azure.microsoft.com/services/?filter=storage) и [сетями Azure](https://azure.microsoft.com/services/?filter=networking);
- опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
- с форматом [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Выберите одну или обе следующие среды создания скриптов, которые понадобятся вам при управлении приложением Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure)

Мы рекомендуем добавить следующие инструменты в среду разработки:

- [Azure Storage Explorer;](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) со следующими расширениями:

  - Расширение: [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Мы также предлагаем проверить доступные средства на странице [Средства для разработчиков Azure](https://azure.microsoft.com/tools/) и, если вы используете Visual Studio, на странице [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Дополнительная информация

[Создание предложения приложения Azure](./cpp-create-offer.md)

