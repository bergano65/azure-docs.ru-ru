---
title: Безопасность
titleSuffix: Azure Cognitive Services
description: Узнайте о различных соображениях безопасности для использования Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131532"
---
# <a name="azure-cognitive-services-security"></a>Безопасность когнитивных служб Azure

Безопасность следует рассматривать как главный приоритет при разработке любых приложений. С началом применения искусственного интеллекта безопасность становится еще более важной. В этой статье описаны различные аспекты безопасности Azure Cognitive Services, такие как использование безопасности транспортного уровня, аутентификация и безопасная настройка конфиденциальных данных.

## <a name="transport-layer-security-tls"></a>Протокол TLS

Все конечные точки Когнитивных Служб, выставленные по http, применяют TLS 1.2. При принудительном соблюдении протокола безопасности потребители, пытающиеся позвонить в конечную точку Cognitive Services, должны придерживаться следующих принципов:

* Клиентская операционная система (ОС) нуждается в поддержке TLS 1.2
* Язык (и платформа), используемый для вызова HTTP, должен указывать TLS 1.2 как часть запроса
  * В зависимости от языка и платформы, указание TLS делается либо неявно, либо явно

Для пользователей .NET рассмотрите <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">рекомендации <span class="docon docon-navigate-external x-hidden-focus"> </span>по безопасности транспортного уровня. </a>

## <a name="authentication"></a>Проверка подлинности

При обсуждении аутентификации существует несколько распространенных заблуждений. Аутентификация и авторизация часто путают друг с другом. Идентичность также является важным компонентом безопасности. Идентификация — это сбор информации о <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span>доверителе. </a> Поставщики идентификационных данных (IdP) предоставляют удостоверения личности службам аутентификации. Аутентификация является актом проверки личности пользователя. Авторизация — это спецификация прав доступа и привилегий к ресурсам для данной идентификации. Некоторые из предложений Cognitive Services включают в себя ролевой контроль доступа (RBAC). RBAC может быть использован для упрощения некоторых церемоний, связанных с ручным управлением принципами. Для получения более подробной информации смотрите [элемент управления доступом на основе ролей для ресурсов Azure.](../role-based-access-control/overview.md)

Для получения дополнительной информации о проверке подлинности с ключами подписки, токенами доступа и активным каталогом Azure (AAD) просрняется запросы на <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">аутентификацию в Azure Cognitive Services.<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="environment-variables-and-application-configuration"></a>Переменные среды и конфигурация приложения

Переменные среды — это пары именных значений, хранящиеся в определенной среде. Более безопасной альтернативой использованию жестких значений для конфиденциальных данных является использование переменных среды. Hardcoded значения являются небезопасными и следует избегать.

> [!CAUTION]
> Не **not** используйте жесткие значения для конфиденциальных данных, это является серьезной уязвимостью безопасности.

> [!NOTE]
> В то время как переменные среды хранятся в простом тексте, они изолированы от среды. Если среда скомпрометирована, то и переменные с окружающей средой тоже.

### <a name="set-environment-variable"></a>Установка переменной среды

Чтобы установить переменные среды, используйте `ENVIRONMENT_VARIABLE_KEY` одну из следующих команд - где является названным ключом и `value` значением, хранящимся в переменной среды.

# <a name="command-line"></a>[Командная линия](#tab/command-line)

Создание и присваивайте сохраняемую переменную среды с учетом значения.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

В новом экземпляре **Командного Запроса**прочитайте переменную среды.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Создание и присваивайте сохраняемую переменную среды с учетом значения.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

В новом экземпляре **Windows PowerShell**прочитайте переменную среды.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Создание и присваивайте сохраняемую переменную среды с учетом значения.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

В новом экземпляре **Bash**прочитайте переменную среды.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> После установки переменной среды перезапустите интегрированную среду разработки (IDE), чтобы обеспечить доступность новых переменных среды.

### <a name="get-environment-variable"></a>Получить переменную среды

Чтобы получить переменную среды, она должна быть прочитана в памяти. В зависимости от языка, который вы используете, рассмотрим следующие фрагменты кода. Эти фрагменты кода демонстрируют, как получить `ENVIRONMENT_VARIABLE_KEY` переменную среды `value`с учетом и назначить переменной с именем.

# <a name="c"></a>[C #](#tab/csharp)

Для получения дополнительной <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Для получения дополнительной <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Для получения дополнительной <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Для получения дополнительной <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Для получения дополнительной <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Для получения дополнительной <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>информации см.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Дальнейшие действия

* Изучите различные [когнитивные услуги](welcome.md)
* Узнайте больше о [виртуальных сетях Cognitive Services](cognitive-services-virtual-networks.md)
