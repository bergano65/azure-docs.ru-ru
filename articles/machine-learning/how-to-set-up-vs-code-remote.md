---
title: 'Интерактивная отладка: VS код & ML вычислять экземпляры'
titleSuffix: Azure Machine Learning
description: Навлаживайте VS Code Remote для интерактивного отладки кода с помощью машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169756"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Отладить интерактивно на Azure machine Learning Compute Instance с помощью VS Code Remote

В этой статье вы узнаете, как настроить Visual Studio Code Remote на Azure Machine Learning Compute Instance, чтобы можно было **интерактивно отладить свой код** из VS Code. 

+ [Azure Machine Learning Compute Instance](concept-compute-instance.md) — это полностью управляемая облачная рабочая станция для ученых, изучающих данные, и предоставляет возможности управления и готовности предприятия для ИТ-администраторов. 


+ [Визуальный код студии удаленного](https://code.visualstudio.com/docs/remote/remote-overview) Разработка позволяет использовать контейнер, удаленную машину или подсистему Windows для Linux (WSL) в качестве полноценной среды разработки. 

## <a name="prerequisite"></a>Предварительные требования  

На платформах Windows необходимо [установить совместимый с OpenSSH клиент SSH,](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) если он еще не присутствует. 

> [!Note]
> PuTTY не поддерживается в Windows, так как команда ssh должна быть в пути. 

## <a name="get-ip-and-ssh-port"></a>Получить IP и SSH порт 

1. Перейти к студии машинного обучения Azure в https://ml.azure.com/.

2. Выберите [рабочее пространство.](concept-workspace.md)
1. Нажмите на вкладку **Вычислить инстанции.**
1. В столбце **Application URI** щелкните **sSH-ссылку** на экземпляр вычислений, который вы хотите использовать в качестве удаленного вычисления. 
1. В диалоге обратите внимание на IP-адрес и порт SSH. 
1. Сохраните свой личный ключ от каталога q/.ssh/ на локальном компьютере; например, откройте редактор для нового файла и вставьте ключ в: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Частный ключ будет выглядеть примерно так:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Изменяйте разрешения в файле, чтобы убедиться, что только вы можете прочитать файл.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Добавление экземпляра в качестве узла 

Откройте `~/.ssh/config` файл (Linux) или `C:\Users<username>.ssh\config` (Windows) в редакторе и добавьте новую запись, похожую на эту:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Вот некоторые подробности на полях: 

|Поле|Описание|
|----|---------|
|Узел|Используйте все, что вам нравится для вычисляемого экземпляра |
|HostName|Это IP-адрес вычисляемого экземпляра |
|Порт|Это порт, показанный на диалоге SSH выше |
|Пользователь|Это должно быть `azureuser` |
|IdentityFile|Следует указать на файл, где вы сохранили закрытый ключ |

Теперь, вы должны мочь ssh к вашему экземпляру `ssh azmlci1`вычисления используя сокращение вы использовали выше, . 

## <a name="connect-vs-code-to-the-instance"></a>Подключение VS-кода к экземпляру 

1. [Установка визуального кода студии](https://code.visualstudio.com/).

1. [Установите удаленное расширение SSH.](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) 

1. Нажмите значок Remote-SSH слева, чтобы показать конфигурации SSH.

1. Нажмите правой кнопкой кнопку только что созданная вами конфигурация sSH-хоста.

1. Выберите **Подключение к хостам в текущем окне.** 

С этого момента вы полностью работаете над вычисляемым экземпляром, и теперь вы можете отобразить, отладить, использовать git, использовать расширения и т.д. - так же, как вы можете с вашим локальным кодом Visual Studio. 

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы создали Visual Studio Code Remote, вы можете использовать экземпляр вычислений в качестве удаленного вычисления от Visual Studio Code для интерактивного отладки кода. 

[Учебник: Тренируйте свою первую модель ML,](tutorial-1st-experiment-sdk-train.md) как использовать экземпляр вычислений с помощью встроенного ноутбука.