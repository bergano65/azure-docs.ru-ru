---
title: Руководство по запуску иммерсивного средства чтения с помощью примеров кода iOS Swift
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить и запустить приложение Swift, которое запускает иммерсивное средство чтения.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049332"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Руководство по запуску иммерсивного средства чтения с помощью примеров кода iOS Swift

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом руководстве показано, как создать приложение iOS, которое запускает иммерсивное средство чтения. В этом руководстве описано следующее:

> [!div class="checklist"]
> * Настройка и запуск приложения Swift для iOS с помощью примера проекта
> * Получение маркера доступа
> * Запуск иммерсивного средства чтения с примером содержимого

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory. Инструкции по настройке см. [здесь](./how-to-create-immersive-reader.md). Вам потребуются некоторые значения, созданные здесь при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/);
* [Пакет SDK иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Определение учетных данных для проверки подлинности

1. В Xcode откройте **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
2. В меню вверху выберите **Product > Scheme > Edit Scheme** (Продукт > Схема > Изменить схему).
3. В представлении **Run** (Запуск) щелкните вкладку **Arguments** (Аргументы).
4. В разделе **Переменные среды** добавьте следующие имена и значения, указав значения, заданные при создании ресурса "Иммерсивное средство чтения".

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Не фиксируйте эти изменения в системе управления версиями, так как они содержат секреты, для которых не следует предоставлять открытый доступ.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

1. В Xcode нажмите клавиши **CTRL+R**, чтобы запустить проект.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
