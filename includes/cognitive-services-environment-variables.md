---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841376"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Настройка переменной среды для проверки подлинности

Приложения должны проходить аутентификацию доступа к Cognitive Services, они используют. Для проверки подлинности, рекомендуется создать переменную среды для хранения ключей для ресурсов Azure. 

После получения ключа, записать в переменную среды на локальном компьютере под управлением приложения. Чтобы задать переменную среды, откройте окно консоли и следуйте инструкциям для используемой операционной системы. Замените `your-key` с одним из ключей для ресурса.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    После добавления переменной среды может потребоваться перезапустить все запущенные приложения, которым может понадобиться считать переменную среды, в том числе окно консоли. Например, если вы используете Visual Studio в качестве редактора, перезапустите Visual Studio перед запуском примера.

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
