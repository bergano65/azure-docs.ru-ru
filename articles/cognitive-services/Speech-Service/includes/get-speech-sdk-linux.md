---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399944"
---
:::row:::
    :::column span="3":::
        Пакет SDK для распознавания речи поддерживает только **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**и **CentOS 7/8** в следующих целевых архитектурах при использовании с Linux:
        - X64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> При нацеливании на Linux ARM64 и использовании C# — требуется пакет .NET Core 3. x (DotNet-SDK-3. x). Если вы намерены ориентироваться на ARM32 или ARM64, Python не поддерживается.

> [!NOTE]
> Архитектуры x86 с Ubuntu 16,04, Ubuntu 18,04 и Debian 9 поддерживают только разработку на C++ с помощью пакета SDK для распознавания речи.

### <a name="system-requirements"></a>Требования к системе

Для собственного приложения речевой пакет SDK полагается на `libMicrosoft.CognitiveServices.Speech.core.so`. Убедитесь, что целевая архитектура (x86, x64) соответствует приложению. В зависимости от версии Linux могут потребоваться дополнительные зависимости.

- общие библиотеки в библиотеке GNU C (включая библиотеку программирования потоков POSIX `libpthreads`);
- Библиотека OpenSSL (`libssl.so.1.0.0` или `libssl.so.1.0.2`)
- общую библиотеку для приложений ALSA (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 и CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Следуйте инструкциям по [настройке RHEL/CentOS 7 для речевого пакета SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Если вы используете RHEL/CentOS 8, выполните инструкции по [настройке OpenSSL для Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
