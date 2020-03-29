---
title: Создание ресурса Персонализатора
description: Конфигурация сервиса включает в себя то, как сервис рассматривает результаты, как часто он исследует, как часто переобучается модель и сколько данных хранится.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336060"
---
# <a name="create-a-personalizer-resource"></a>Создание ресурса Personalizer

Ресурс Personalizer — это то же самое, что и цикл обучения Personalizer. Для каждого домена или области содержимого, которая у вас есть, создается единый ресурс, или цикл обучения. Не используйте несколько областей содержимого в одном цикле, поскольку это запутает цикл обучения и обеспечит плохие прогнозы.

Если вы хотите, чтобы Personalizer выбрал лучшее содержимое для более чем одной области содержимого веб-страницы, используйте другой цикл обучения для каждого из них.


## <a name="create-a-resource-in-the-azure-portal"></a>Создание ресурса на портале Azure

Создайте ресурс Персонализатора для каждого цикла обратной связи.

1. Войти на [портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Предыдущая ссылка ведет на страницу **Создание** Персонализатора.
1. Введите имя службы, выберите подписку, расположение, ценовую категорию и группу ресурсов.

    > [!div class="mx-imgBorder"]
    > ![Используйте портал Azure для создания ресурса Personalizer, также называемого циклом обучения.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Выберите **Создать** для создания ресурса.

1. После развертывания ресурса выберите кнопку **«Перейти к ресурсу»,** чтобы перейти на ресурс Personalizer.

1. Выберите быструю **стартовую** страницу для вашего ресурса, а затем скопируйте значения для вашей конечных точек и ключа. Для использования AA-иаут ранга и вознаграждения необходимо как конечная точка ресурса, так и ключ.

1. Выберите страницу **конфигурации** для нового ресурса для [настройки цикла обучения.](how-to-settings.md)

## <a name="create-a-resource-with-the-azure-cli"></a>Создание ресурса с помощью Azure CLI

1. Вопийте в Azure CLI со следующей командой:

    ```azurecli-interactive
    az login
    ```

1. Создайте группу ресурсов, логическую группу для управления всеми ресурсами Azure, которые вы собираетесь использовать с ресурсом Personalizer.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Создайте новый ресурс Personalizer, _обучающий цикл_со следующей командой для существующей группы ресурсов.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Это возвращает объект JSON, который включает **конечную точку ресурса.**

1. Используйте следующую команду Azure CLI, чтобы получить **ключ ресурса.**

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Для использования AA-иаут ранга и вознаграждения необходимо как конечная точка ресурса, так и ключ.

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка](how-to-settings.md) Цикл обучения персонализатора