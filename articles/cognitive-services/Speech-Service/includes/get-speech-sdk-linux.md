---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a93c478f0621bf62b710f58f3e6f06298bad9954
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673056"
---
:::row:::
    :::column span="3":::
        Пакет SDK для распознавания речи поддерживает только **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**и **CentOS 7/8** в следующих целевых архитектурах при использовании с Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) и ARM64 (Debian/Ubuntu) для разработки на C++
- x64, ARM32 (Debian/Ubuntu) и ARM64 (Debian/Ubuntu) для Java
- x64, ARM32 (Debian/Ubuntu) и ARM64 (Debian/Ubuntu) для .NET Core
- x64 для Python

> [!IMPORTANT]
> Для C# в ARM64 Linux требуется пакет .NET Core 3. x (DotNet-SDK-3. x).

### <a name="system-requirements"></a>Требования к системе

Для собственного приложения речевой пакет SDK полагается на `libMicrosoft.CognitiveServices.Speech.core.so` . Убедитесь, что целевая архитектура (x86, x64) соответствует приложению. В зависимости от версии Linux могут потребоваться дополнительные зависимости.

- общие библиотеки в библиотеке GNU C (включая библиотеку программирования потоков POSIX `libpthreads`);
- Библиотека OpenSSL ( `libssl.so.1.0.0` или `libssl.so.1.0.2` )
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
> - В RHEL/CentOS 7 выполните инструкции по [настройке пакета SDK для RHEL/CentOS 7 для распознавания речи](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Если вы используете RHEL/CentOS 8, выполните инструкции по [настройке OpenSSL для Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
