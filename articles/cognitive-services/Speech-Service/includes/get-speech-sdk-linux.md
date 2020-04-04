---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a298e78c32464680dab9feccb3cfc84f686ff81b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656564"
---
:::row:::
    :::column span="3":::
        Речь SDK поддерживает только **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 8**, и **CentOS 7/8** на следующих целевых архитектур при использовании с Linux:
        - x86
        - X64
        - ARM32
        - ARM64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> При таргетинге на Linux ARM64 и использовании C-класса требуется .NET Core 3.x (пакет dotnet-sdk-3.x). Если вы ориентируетесь на ARM32 или ARM64, Python не поддерживается.

> [!NOTE]
> Архитектуры x86 Ubuntu 16.04, Ubuntu 18.04 и Debian 9 поддерживают только разработку C's с помощью Speech SDK.

### <a name="system-requirements"></a>Требования к системе

Для родного приложения, речь SDK `libMicrosoft.CognitiveServices.Speech.core.so`опирается на . Убедитесь, что целевая архитектура (x86, x64) соответствует приложению. В зависимости от версии Linux могут потребоваться дополнительные зависимости.

- общие библиотеки в библиотеке GNU C (включая библиотеку программирования потоков POSIX `libpthreads`);
- Библиотека OpenSSL`libssl.so.1.0.0` (или `libssl.so.1.0.2`)
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

# <a name="rhel-8-and-centos-78"></a>[RHEL 8 и CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Следуйте инструкциям о [том, как настроить RHEL/CentOS 7 для речи SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> На RHEL/CentOS 8 следуйте инструкциям о том, [как настроить OpenSSL для Linux.](../how-to-configure-openssl-linux.md)

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
