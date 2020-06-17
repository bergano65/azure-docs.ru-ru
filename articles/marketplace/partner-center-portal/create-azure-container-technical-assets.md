---
title: Технические концепции предложений по контейнерам Azure. Коммерческий каталог Marketplace от Майкрософт
description: Статья содержит технические ресурсы и рекомендации, которые помогут настроить предложение по контейнерам в Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700980"
---
# <a name="create-an-azure-container-offer"></a>Создание предложения по контейнерам Azure

Эта статья содержит технические ресурсы и рекомендации, которые помогут настроить предложение по контейнерам в Azure Marketplace.

## <a name="before-you-begin"></a>Перед началом

Ознакомьтесь с [документацией по службе "Экземпляры контейнеров Azure"](https://docs.microsoft.com/azure/container-instances). Там вы найдете краткие инструкции, руководства и примеры для начала работы.

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, сборка и тестирование используемых ресурсов занимают время и требуют технических знаний не только платформы Azure, но и технологий, используемых для создания предложения.

Ваша команда разработчиков должна не только хорошо разбираться во всем, что касается вашего решения, но и обладать знаниями о технологиях Майкрософт. В частности, вашим специалистам потребуется следующее:

- базовое представление о [службах Azure](https://azure.microsoft.com/services/);
- умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
- практические знания о [Виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/), [Службе хранилища Azure](https://azure.microsoft.com/services/?filter=storage) и [Сетях Azure](https://azure.microsoft.com/services/?filter=networking);
- опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
- практическое умение использовать [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Для управления образами контейнеров рекомендуем использовать следующие среды сценариев:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1);
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Кроме того, рекомендуем добавить в среду разработки следующие инструменты:

- [Обозреватель службы хранилища Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Расширение: [Средства Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Просмотрите доступные средства на странице [средств для разработчиков Azure](https://azure.microsoft.com/). Если вы используете Visual Studio, просмотрите доступные средства в [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

Дополнительные сведения см. в следующих руководствах:

- [Руководство. Создание образа контейнера для развертывания в службе "Экземпляры контейнеров Azure"](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Руководство. Создание и развертывание образов контейнера в облаке с помощью службы "Задачи Реестра контейнеров Azure"](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Дальнейшие действия

- [Создание предложения по контейнерам](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
