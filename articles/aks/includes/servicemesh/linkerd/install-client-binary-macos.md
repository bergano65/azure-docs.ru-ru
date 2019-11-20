---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a4090172e926f21db01a8e374d8c4bb1c80402c7
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74197375"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Скачивание и установка двоичного файла клиента Linkerd Linkerd

В оболочке на основе Bash в MacOS используйте `curl` для загрузки выпуска Linkerd следующим образом:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd` клиентский двоичный файл выполняется на клиентском компьютере и позволяет взаимодействовать с сеткой службы Linkerd. Используйте следующие команды, чтобы установить двоичный файл клиента Linkerd `linkerd` в оболочке на основе Bash в MacOS. Эти команды копируют двоичный файл клиента `linkerd` в стандартное расположение программ для пользователя в `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Если вы хотите задать завершение командной строки для двоичного файла клиента Linkerd `linkerd`, настройте его следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
