---
title: 'Интерактивная отладка: VS Code &ных экземпляров машинного обучения ML'
titleSuffix: Azure Machine Learning
description: Настройка VS Code удаленного для интерактивной отладки кода с помощью Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77169756"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Отладка в интерактивном режиме на Машинное обучение Azure вычислительного экземпляра с VS Code удаленно

В этой статье вы узнаете, как настроить Visual Studio Code удаленно на Машинное обучение Azure вычислительного экземпляра, чтобы можно было выполнять **интерактивную отладку кода** из VS Code. 

+ [Вычислительный экземпляр машинное обучение Azure](concept-compute-instance.md) — это полностью управляемая облачная Рабочая станция для специалистов по обработке и анализу данных, которая предоставляет ИТ-администраторам возможности управления и готовности предприятия. 


+ [Visual Studio Code удаленный](https://code.visualstudio.com/docs/remote/remote-overview) Разработка позволяет использовать контейнер, удаленный компьютер или подсистему Windows для Linux (WSL) в качестве полнофункциональной среды разработки. 

## <a name="prerequisite"></a>Предварительные требования  

На платформах Windows необходимо [установить клиент SSH, совместимый с OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , если он еще не существует. 

> [!Note]
> Выводимое значение не поддерживается в Windows, так как команда SSH должна находиться в пути. 

## <a name="get-ip-and-ssh-port"></a>Получение IP-адреса и порта SSH 

1. Перейдите в Машинное обучение Azure Studio по адресу https://ml.azure.com/.

2. Выберите [рабочую область](concept-workspace.md).
1. Перейдите на вкладку « **экземпляры вычислений** ».
1. В столбце **URI приложения** щелкните ссылку **SSH** для вычислительного экземпляра, который требуется использовать в качестве удаленного вычислений. 
1. В диалоговом окне запишите IP-адрес и порт SSH. 
1. Сохраните закрытый ключ в каталоге ~/.СШ/на локальном компьютере. Например, откройте редактор для нового файла и вставьте ключ в: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Закрытый ключ будет выглядеть примерно так:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Измените разрешения для файла, чтобы убедиться, что файл доступен только для чтения.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Добавление экземпляра в качестве узла 

Откройте файл `~/.ssh/config` (Linux) или `C:\Users<username>.ssh\config` (Windows) в редакторе и добавьте новую запись, подобную следующей:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Ниже приведены некоторые сведения о полях. 

|Поле|Описание|
|----|---------|
|Узел|Использование любой краткой формы для вычислительного экземпляра |
|HostName|Это IP-адрес вычислительного экземпляра |
|Порт|Это порт, показанный в диалоговом окне SSH выше |
|User (Пользователь)|Это должно быть `azureuser` |
|идентитифиле|Должен указывать на файл, в который был сохранен закрытый ключ |

Теперь вы можете подключиться к вычислительному экземпляру по протоколу SSH, используя сокращенную версию, `ssh azmlci1`которую вы использовали ранее. 

## <a name="connect-vs-code-to-the-instance"></a>Подключение VS Code к экземпляру 

1. [Установите Visual Studio Code](https://code.visualstudio.com/).

1. [Установите удаленное расширение SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Щелкните значок Remote-SSH слева, чтобы отобразить конфигурации SSH.

1. Щелкните правой кнопкой мыши только что созданную конфигурацию узла SSH.

1. Выберите **Подключение к узлу в текущем окне**. 

С этого момента вы полностью работаете с вычислительным экземпляром, и теперь можете редактировать, отлаживать, использовать Git, использовать расширения и т. д., как и в случае с локальными Visual Studio Code. 

## <a name="next-steps"></a>Дальнейшие шаги

Теперь, когда вы настроили Visual Studio Code удаленно, вы можете использовать вычислительный экземпляр в качестве удаленного вычислений от Visual Studio Code для интерактивной отладки кода. 

[Учебник. обучение первой модели машинного обучения](tutorial-1st-experiment-sdk-train.md) демонстрирует использование вычислительного экземпляра с интегрированной записной книжкой.