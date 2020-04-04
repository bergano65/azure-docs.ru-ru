---
title: Как настроить RHEL/CentOS 7 - Речевой сервис
titleSuffix: Azure Cognitive Services
description: Узнайте, как настроить RHEL/CentOS 7 так, чтобы можно было использовать Speech SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639166"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Настройка RHEL/CentOS 7 для речи SDK

Red Hat Enterprise Linux (RHEL) 8 x64 и CentOS 8 x64 официально поддерживаются версией Speech SDK 1.10.0 и позже. Кроме того, можно использовать Speech SDK на RHEL/CentOS 7 x64, но это требует обновления компилятора СЗ (для разработки СЗ) и общей библиотеки времени выполнения в вашей системе.

Чтобы проверить версию компилятора СЗ, запустите:

```bash
g++ --version
```

Если компилятор установлен, выход должен выглядеть следующим образом:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Это сообщение позволяет узнать, что gCC основная версия 4 установлена. Эта версия не имеет полной поддержки стандарта СЗ 11, который использует Speech SDK. Попытка компилировать программу СЗ с этой версией GCC и заголовками Speech SDK приведет к ошибкам компиляции.

Также важно проверить версию общей библиотеки времени выполнения C '(libstdc). Большая часть Речи SDK реализована как родная библиотека СЗ, что означает, что она зависит от libstdc, независимо от языка, который вы используете для разработки приложений.

Чтобы найти местоположение libstdc в вашей системе, запустите:

```bash
ldconfig -p | grep libstdc++
```

Выход на ваниль RHEL/CentOS 7 (x64):

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

На основе этого сообщения необходимо проверить определения версии с помощью этой команды:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Результат должен выглядеть примерно так:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Речь SDK требует **CXXABI_1.3.9** и **GLIBCXX_3.4.21**. Вы можете найти эту `ldd libMicrosoft.CognitiveServices.Speech.core.so` информацию, запустив на речевой SDK библиотеки из пакета Linux.

> [!NOTE]
> Рекомендуется, чтобы версия GCC, установленная на системе, не менее **5.4.0**, с соответствующими библиотеками времени выполнения.

## <a name="example"></a>Пример

Это пример команды, которая иллюстрирует, как настроить RHEL/CentOS 7 x64 для разработки (КЗ, КЗ, Ява, Python) с речью SDK 1.10.0 или позже:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о пакете SDK службы "Речь"](speech-sdk.md)
