---
title: Устранение неполадок с образами среды
titleSuffix: Azure Machine Learning
description: Узнайте, как устранять неполадки при сборке образов среды и установке пакетов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733397"
---
# <a name="troubleshoot-environment-image-builds"></a>Устранение неполадок сборок образов среды
Узнайте, как устранять проблемы с сборками образов среды DOCKER и установками пакетов.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).
* [Пакет SDK для Машинного обучения Azure](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Интерфейс командной строки Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Расширение CLI для Машинного обучения Azure](reference-azure-machine-learning-cli.md).
* Для локальной отладки вам необходима рабочая установка Docker в локальной системе.

## <a name="docker-image-build-failures"></a>Сбои сборки образа DOCKER
 
В большинстве сбоев сборки образа основная причина может быть найдена в журнале сборки образа.
Журнал сборки образа можно найти на портале Машинное обучение Azure (20 \_ образов \_ сборки \_log.txt) или в ЗАДАЧах записи контроля доступа запуск журналов.
 
В большинстве случаев проще воспроизвести ошибки локально. Проверьте тип ошибки и попробуйте выполнить одно из следующих `setuptools` действий.

- Установить зависимость conda локально `conda install suspicious-dependency==X.Y.Z`
- Установить зависимость PIP локально `pip install suspicious-dependency==X.Y.Z`
- Попробуйте материализовать всю среду `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Убедитесь, что платформа и интерпретатор на локальном вычислений совпадают с теми, которые находятся на удаленном компьютере. 

### <a name="timeout"></a>Время ожидания 
 
Проблемы времени ожидания могут возникать в различных сетевых проблемах.
- Низкая пропускная способность Интернета
- Проблемы с сервером
- Большая зависимость, которую невозможно загрузить с заданными параметрами времени ожидания conda или PIP
 
Сообщения, аналогичные приведенным ниже, указывают на ошибку:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Возможные решения:
 
- Попробуйте использовать другой источник зависимости, если он доступен, например зеркала, хранилище BLOB-объектов или другие каналы Python.
- Обновите conda или PIP. Если используется пользовательский файл DOCKER, обновите параметры времени ожидания.
- Некоторые версии PIP имеют известные проблемы. Рассмотрите возможность добавления определенной версии PIP в зависимости среды.

### <a name="package-not-found"></a>Пакет не найден

Это наиболее распространенный случай сбоев сборки образа.

- Не удалось найти пакет Conda
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Не удалось найти указанный пакет PIP или версию
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Неправильная вложенная зависимость PIP
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Убедитесь, что пакет существует в указанных источниках. Используйте [Поиск PIP](https://pip.pypa.io/en/stable/reference/pip_search/) для проверки зависимостей PIP.

`pip search azureml-core`

Для зависимостей conda используйте [Поиск conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Дополнительные параметры:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Примечания к установщику

Убедитесь, что для указанной платформы и версии интерпретатора Python существует необходимое распределение.

Для зависимостей PIP перейдите к, `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` чтобы узнать, доступна ли нужная версия. Например https://pypi.org/project/azureml-core/1.11.0/#files.

Для зависимостей conda Проверьте пакет в репозитории канала.
Для каналов, поддерживаемых Anaconda, Inc., установите [здесь](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Обновление пакета PIP

Во время установки или обновления пакета PIP сопоставительу может потребоваться обновить уже установленный пакет для удовлетворения новых требований.
Удаление может завершиться сбоем по различным причинам, связанным с версией PIP, или способом установки зависимости.
Наиболее распространенным сценарием является то, что зависимость, установленная conda, не может быть удалена с помощью PIP.
Для этого сценария рассмотрите возможность удаления зависимости с помощью `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Проблемы с установщиком

В некоторых версиях установщика возникают проблемы в процессе поиска пакетов, которые могут привести к сбою сборки.

Если используется пользовательский базовый образ или dockerfile, рекомендуется использовать conda версии 4.5.4 или выше.

Для установки зависимостей PIP требуется пакет PIP, и если в среде не указана версия, будет использоваться последняя версия.
Рекомендуется использовать известную версию PIP, чтобы избежать временных проблем или критических изменений, которые могут быть вызваны последней версией средства.

Рассмотрите возможность закрепления версии PIP в вашей среде, если вы видите любое из сообщений, приведенных ниже:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Кроме того, при наличии неразрешимых конфликтов в зависимостях установка PIP может быть задержана в бесконечном цикле. При работе в локальной среде понизить версию PIP до < 20,3. В среде conda, созданной из файла YAML, эта проблема будет отображаться только в том случае, если conda — подделывать канал с наивысшим приоритетом. Чтобы устранить эту ошибку, явно укажите PIP < 20,3 (! = 20,3 или = 20.2.4 ПИН-код для другой версии) в качестве зависимости conda в файле спецификации conda.

## <a name="service-side-failures"></a>Сбои на стороне службы

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Не удалось разрешить Извлечение изображения из мкр/Address для реестра контейнеров.
Возможные проблемы:
- Имя пути к реестру контейнеров может быть неправильно устранено. Убедитесь, что в именах образов используются двойные косые черты и направление косой черты на узлах Linux VS Windows верно.
- Если запись контроля доступа за виртуальной сетью использует закрытую конечную точку в [неподдерживаемом регионе](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), настройте запись контроля доступа для виртуальной сети с помощью конечной точки службы (открытый доступ) на портале и повторите попытку.
- После помещения записи контроля доступа за пределы виртуальной сети убедитесь, что [шаблон ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) запущен. Это позволяет рабочей области взаимодействовать с экземпляром записи контроля доступа.

### <a name="401-error-from-workspace-acr"></a>401. ошибка в записи контроля доступа рабочей области
Повторная синхронизация ключей хранилища с помощью [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>Среда продолжает создавать "ожидание завершения других операций Conda..." План
Когда сборка образа выполняется, conda блокируется клиентом SDK. Если процесс завершился сбоем или был неверно отменен пользователем-conda остается в заблокированном состоянии. Чтобы устранить эту проблему, удалите файл блокировки вручную. 

### <a name="custom-docker-image-not-in-registry"></a>Пользовательский образ DOCKER отсутствует в реестре
Проверьте, используется ли [правильный тег](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` отключает Conda и использует установленные пользователем пакеты.

### <a name="common-vnet-issues"></a>Распространенные проблемы виртуальной сети

1. Убедитесь, что учетная запись хранения, кластер вычислений и реестр контейнеров Azure находятся в одной подсети виртуальной сети.
2. Если запись контроля доступа находится за виртуальной сетью, ее нельзя использовать для сборки образов напрямую. Для создания образов необходимо использовать кластеры вычислений.
3. Может потребоваться разместить хранилище за виртуальной сетью в следующих случаях:
    - Использование закрестного или частного колеса
    - Просмотр ошибок службы 403 (не санкционированных)
    - Не удается получить сведения об образе из записи контроля доступа или мкр

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Сбой сборки образа при попытке доступа к защищенному сетевым хранилищем
- Задачи записи контроля доступа не работают за виртуальной сетью. Если у пользователя есть запись контроля доступа за виртуальную сеть, для создания образа необходимо использовать этот кластер.
- Хранилище должно находиться за виртуальной сетью, чтобы иметь возможность извлекать из нее зависимости. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Невозможно запустить эксперименты, если для хранилища включена безопасность сети
При использовании образов DOCKER по умолчанию и включении зависимостей, управляемых пользователем, необходимо использовать [теги службы](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) Микрософтконтаинеррегистри и Азурефронтдур. фирстпарти для разрешенных мкр и его зависимостей.

 Дополнительные сведения см. в разделе [Включение виртуальной сети](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) .

### <a name="creating-an-icm"></a>Создание ICM

При создании или назначении ICM для хранилище метаданных включите запрос в службу поддержки CSS, чтобы мы могли лучше понять эту ошибку.

## <a name="next-steps"></a>Дальнейшие действия

- [Обучение модели машинного обучения для категоризации цветов](how-to-train-scikit-learn.md)
- [Обучение модели машинного обучения с помощью пользовательского образа DOCKER](how-to-train-with-custom-image.md)