---
title: Создание технического актива Azure VM
description: В этой статье описаны шаги и требования для настройки контейнерного предложения на Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730637"
---
# <a name="create-an-azure-vm-technical-asset"></a>Создание технического актива Azure VM

> [!IMPORTANT]
> Мы перемещаем управление предложениями Вашего контейнера Azure с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, следуйте инструкциям в [подготовке технических активов контейнера](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) для Cloud Partner Portal для управления вашими предложениями.

В этой статье описаны шаги и требования для настройки предложения контейнеров на Azure Marketplace.

## <a name="before-you-begin"></a>Подготовка к работе

Для получения быстрых запусков, [Azure Container Instances](https://docs.microsoft.com/azure/container-instances)учебников и образцов см.

## <a name="fundamental-technical-knowledge"></a>Основные технические знания

Проектирование, создание и тестирование этих ресурсов требует времени и технических знаний как платформы Azure, так и технологий, используемых для создания предложения.

В дополнение к домену решения, ваша команда инженеров должна иметь знания о следующих технологиях Майкрософт:

- базовое представление о [службах Azure](https://azure.microsoft.com/services/);
- умение [разработать приложения Azure](https://azure.microsoft.com/solutions/architecture/);
- Рабочие знания [виртуальных машин Azure,](https://azure.microsoft.com/services/virtual-machines/) [хранения azure](https://azure.microsoft.com/services/?filter=storage)и [сети Azure](https://azure.microsoft.com/services/?filter=networking)
- опыт работы с [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/);
- Рабочие знания [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Рекомендуемые инструменты

Выберите одну или обе из следующих сред скриптов, чтобы помочь управлять изображением контейнера:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Лазурный CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Мы рекомендуем добавить эти инструменты в среду разработки:

- [Обозреватель службы хранилища Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Расширение: [Инструменты Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).
  - Расширение: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify).
  - Расширение: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Просмотрите доступные инструменты на странице [Azure Developer Tools.](https://azure.microsoft.com/) Если вы используете Visual Studio, просмотрите инструменты, доступные в [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Создание образа контейнера

Для получения дополнительной информации, см.

- [Учебник: Создание изображения контейнера для развертывания в экземплярах контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Учебник: Создание и развертывание изображений контейнеров в облаке с помощью задач реестра контейнеров Azure.](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-step"></a>Следующий шаг

- [Создайте предложение контейнера.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)
