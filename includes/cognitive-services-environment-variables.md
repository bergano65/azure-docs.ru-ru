---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461500"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Настройка переменной среды для проверки подлинности

Приложения должны проходить аутентификацию доступа к Cognitive Services, они используют. Для проверки подлинности, рекомендуется создать переменную среды для хранения ключа в списке ресурсов Azure. 

После получения ключа, записать в переменную среды на локальном компьютере под управлением приложения. Чтобы задать переменную среды, откройте окно консоли и следуйте инструкциям для используемой операционной системы. Замените `your-key` ключом доступа своей обнаружения аномалий:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    После добавления переменной среды может потребоваться перезапустить все запущенные приложения, которым может понадобиться считать переменную среды, в том числе окно консоли. Например если вы используете Visual Studio в качестве редактора, перезапустите Visual Studio до запуска примера.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.
    
* macOS
    
    Измените .bash_profile и добавьте переменную среды.
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.
