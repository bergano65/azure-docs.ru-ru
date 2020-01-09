---
title: Установка пакетов в записных книжках Jupyter в предварительной версии Azure Notebook
description: Узнайте, как установить Python, R и F# пакеты в записной книжке Jupyter, работающей в Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646234"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Установка пакетов из в предварительной версии Azure Notebook Preview

Несмотря на то что можно настроить [среду для записной книжки на уровне проекта](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), может потребоваться установить пакеты непосредственно в отдельной записной книжке.

Пакеты, установленные в записной книжке, применяются только к текущему сеансу сервера. Установки пакетов не сохраняются после завершения работы сервера.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

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

Затем загрузите генератор пакет:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Откройте библиотеку:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Дальнейшие действия

- [Как настраивать проекты и управлять ими](configure-manage-azure-notebooks-projects.md)
- [Как Показать слайд-шоу](present-jupyter-notebooks-slideshow.md)
