---
title: Руководство по запуску иммерсивного средства чтения с помощью примеров кода Android
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить и запустить приложение Android, которое запускает иммерсивное средство чтения.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516441"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Руководство по запуску иммерсивного средства чтения с помощью примера кода Java для Android

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом учебнике описано, как создать приложение Android, которое запускает иммерсивное средство чтения. В этом руководстве описано следующее:

> [!div class="checklist"]
> * Настройка и запуск приложения для Android с помощью примера проекта.
> * Получение маркера доступа.
> * Запуск иммерсивного средства чтения с примером содержимого.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory. Инструкции по настройке см. [здесь](./how-to-create-immersive-reader.md). Некоторые из созданных здесь значений вам потребуются при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [Git](https://git-scm.com/).
* [Пакет SDK иммерсивного средства чтения.](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Определение учетных данных для проверки подлинности

1. Запустите Android Studio и откройте проект в каталоге **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) или **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Создайте файл **env** в папке **/assets**. Добавьте следующие имена и значения, используя данные о вашей среде. Не фиксируйте этот файл в системе управления версиями, так как он содержит секреты, не предназначенные для публикации.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

Выберите эмулятор устройства в диспетчере AVD и запустите проект.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справкой по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).