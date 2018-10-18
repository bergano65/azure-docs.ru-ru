---
title: Внедрение Azure Cloud Shell | Документация Майкрософт
description: Узнайте, как внедрить Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 37ad0adf01172fadc6d9845576045a4542bdae19
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497796"
---
# <a name="embed-azure-cloud-shell"></a>Внедрение Azure Cloud Shell

Внедрение Cloud Shell позволяет разработчикам и авторам напрямую открывать Cloud Shell по выделенному URL-адресу — [shell.azure.com](https://shell.azure.com). Пользователям сразу же станут доступны возможности аутентификации Cloud Shell, инструментарий и актуальные инструменты Azure CLI и Azure PowerShell.

Стандартная по размеру кнопка

[![Обычная кнопка запуска](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)

Большая по размеру кнопка

[![Большая кнопка запуска](https://shell.azure.com/images/launchcloudshell@2x.png "Запуск Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Инструкции

Интегрируйте кнопку запуска Cloud Shell в файлы Markdown, скопировав следующий фрагмент кода:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Ниже приведен HTML для внедрения всплывающего меню Cloud Shell:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Настройка среды

Задайте определенную оболочку с помощью расширения URL-адреса.
|Среда   |URL-адрес   |
|---|---|
|Последняя используемая оболочка   |[shell.azure.com](https://shell.azure.com);           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по использованию Bash в Azure Cloud Shell](quickstart.md)<br>
[Краткое руководство по использованию PowerShell в Azure Cloud Shell (предварительная версия)](quickstart-powershell.md)
