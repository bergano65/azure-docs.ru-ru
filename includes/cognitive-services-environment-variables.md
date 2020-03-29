---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274674"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Настройка переменной среды для проверки подлинности

Приложениям необходимо проверить подлинность доступа к использовательным службам, которые они используют. Для проверки подлинности рекомендуется создать переменную среды для хранения ключей для ресурсов Azure. 

После того, как у вас есть ключ, напишите его в новую переменную среды на локальной машине, работая над приложением. Чтобы задать переменную среды, откройте окно консоли и следуйте инструкциям для используемой операционной системы. Замените `your-key` один из ключей для вашего ресурса.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

После добавления переменной среды может потребоваться перезапустить все запущенные приложения, которым может понадобиться считать переменную среды, в том числе окно консоли. Например, если вы используете Visual Studio в качестве редактора, перезапустите Visual Studio перед запуском примера.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macos"></a>[macOS](#tab/unix)

Измените .bash_profile и добавьте переменную среды.

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.

***