---
title: Установка пакетов в записной книжке Jupyter в Azure
description: Установка пакетов Python, R и F# из записной книжки Jupyter, запущенной в Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: d18a33b7eeb1e3708b4e657180421bd97dc268b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764343"
---
# <a name="install-packages-from-within-a-notebook"></a>Установка пакетов из записной книжки

Несмотря на то что можно настроить [среду для записной книжки на уровне проекта](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), может потребоваться установить пакеты непосредственно в отдельной записной книжке.

Пакеты, установленные в записной книжке, применяются только к текущему сеансу сервера. Установки пакетов не сохраняются после завершения работы сервера.

## <a name="python"></a>Python

Пакеты на языке Python можно установить с помощью pip или conda, используя команды в пределах ячеек кода:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Если выходные данные команды указывают, что требование уже выполнено, возможно, Записные книжки Azure включают нужный пакет по умолчанию. Пакет также может устанавливаться в рамках [шага настройки среды проекта](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Пакеты на языке R можно установить из CRAN или GitHub с помощью функции `install.packages` в ячейке кода:

```r
install.packages("package_name")
```

Можно также установить предварительные версии и другие пакеты разработки с сайта GitHub с помощью библиотеки devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Пакеты на F# можно установить из [nuget.org](https://www.nuget.org) путем вызова диспетчера зависимостей Paket из ячеек кода. Сначала загрузите диспетчер Paket:

```fsharp
#load "Paket.fsx"
```

Затем установите пакеты:

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>Дальнейшие действия

- [Практическое руководство. Настройка проектов и управление ими](configure-manage-azure-notebooks-projects.md)
- [Практическое руководство по представлению слайд-шоу](present-jupyter-notebooks-slideshow.md)
