---
title: Руководство по Запуск иммерсивного средства чтения с помощью примера кода iOS Swift
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить и запустить пример приложения Swift, которое запускает иммерсивное средство чтения.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516373"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Руководство по Запуск иммерсивного средства чтения с помощью примера кода iOS Swift

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом руководстве показано, как создать приложение iOS, которое запускает иммерсивное средство чтения. В этом руководстве описано следующее:

> [!div class="checklist"]
> * Настройка и запуск приложения Swift для iOS с помощью примера проекта.
> * Получение маркера доступа.
> * Запуск иммерсивного средства чтения с примером содержимого.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory. Инструкции по настройке см. [здесь](./how-to-create-immersive-reader.md). Некоторые из созданных здесь значений вам потребуются при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Пакет SDK иммерсивного средства чтения.](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode.](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Определение учетных данных для проверки подлинности

1. В Xcode откройте **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. В верхнем меню выберите **Product (Продукт)**  > **Scheme (Схема)**  > **Edit Scheme (Изменить схему)** .
1. В представлении **Run** (Запуск) откройте вкладку **Arguments** (Аргументы).
1. В разделе **Environment Variables** (Переменные среды) добавьте следующие имена и значения. Укажите значения, заданные при создании ресурса "Иммерсивное средство чтения".

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Не фиксируйте это изменение в системе управления версиями, так как оно содержит секреты, не предназначенные для публикации.

## <a name="start-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

В Xcode нажмите клавиши **CTRL+R**, чтобы запустить проект.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справкой по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
