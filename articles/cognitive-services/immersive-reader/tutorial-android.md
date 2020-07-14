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
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049326"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Руководство по запуску иммерсивного средства чтения с помощью примера кода Java

В [обзорной статье](./overview.md) вы узнали о том, что представляет собой иммерсивное средство чтения и каким образом в нем реализованы проверенные методы, улучшающие понимание прочитанного для начинающих, тех, кто изучает язык, и учащихся с особыми потребностями. В этом руководстве показано, как создать приложение Android, которое запускает иммерсивное средство чтения. В этом руководстве описано следующее:

> [!div class="checklist"]
> * Настройка и запуск приложения для Android с помощью примера проекта
> * Получение маркера доступа
> * Запуск иммерсивного средства чтения с примером содержимого

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory. Инструкции по настройке см. [здесь](./how-to-create-immersive-reader.md). Вам потребуются некоторые значения, созданные здесь при настройке свойств среды. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.
* [Git](https://git-scm.com/);
* [Пакет SDK иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Определение учетных данных для проверки подлинности

1. Запустите Android Studio и откройте проект в каталоге **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) или **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

2. Создайте файл с именем **env** в папке **/assets** и добавьте следующий код с соответствующими значениями. Не забудьте зафиксировать этот файл в системе управления версиями, так как он содержит секреты, для которых не следует предоставлять открытый доступ.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Запуск иммерсивного средства чтения с примером содержимого

1. Выберите эмулятор устройства в диспетчере AVD и запустите проект.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](./reference.md).
* Просмотрите примеры кода на сайте [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).