---
title: Использование действий GitHub с синхронизацией конфигурации приложений Azure
description: Использование действий GitHub для активации обновления экземпляра конфигурации приложения при выполнении определенных действий в репозитории GitHub
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1b7586e6321318a3fe4566e8c744c17a1d7ee179
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310628"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Синхронизация экземпляра конфигурации приложения с помощью действий GitHub
В конфигурации приложений Azure используются действия GitHub для обновления экземпляра конфигурации приложения при срабатывании действия, выполняемого в репозитории GitHub. Вы можете использовать рабочие процессы GitHub для обновления конфигурации приложения, обеспечивая интеграцию обновлений конфигурации приложений в тот же рабочий процесс, который используется для обновления кода приложения.

[Рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) GitHub Actions — это автоматизированный процесс, определенный в репозитории GitHub. Этот процесс говорит GitHub о том, как создать и развернуть проект GitHub. Конфигурация приложений Azure предоставляет действие *синхронизации конфигурации приложения Azure* , которое позволяет включить обновление экземпляра конфигурации приложения при внесении изменений в исходный репозиторий. 

Рабочий процесс определяется файлом YAML (yml), найденным в `/.github/workflows/` пути к репозиторию. Это определение содержит различные шаги и параметры, определяющие рабочий процесс.

События GitHub, такие как отправка в репозиторий, могут активировать рабочий процесс действия GitHub.  Azure предоставляет действие *синхронизации конфигурации приложения Azure* , которое позволяет активировать обновление экземпляра конфигурации приложения при выполнении указанного действия GitHub. Это позволяет группам использовать основные функции GitHub при отправке, просмотре или ветвлении файлов конфигурации приложений точно так же, как и при работе с кодом приложения.

[Документация](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) по GitHub содержит подробные сведения о рабочих процессах и действиях GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Включение действий GitHub в репозитории
Чтобы начать работу с этим действием GitHub, перейдите в репозиторий и перейдите на вкладку " **действия** ". Найдите и выберите действие GitHub в Marketplace, выполнив поиск по фразе "Синхронизация конфигурации приложений Azure". 

> [!div class="mx-imgBorder"]
> ![перейдите на вкладку Действие](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![выберите действие SYN конфигурации приложения](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Синхронизация файлов конфигурации после отправки
Это действие синхронизирует файлы конфигурации приложений Azure при отправке изменений в `appsettings.json`. Когда разработчик выполняет и отправляет изменения в `appsettings.json`, действие синхронизации конфигурации приложения обновляет экземпляр конфигурации приложения новыми значениями.

В первом разделе этого рабочего процесса указывается, что действие активируется *при* *отправке* , содержащем `appsettings.json` в *главную* ветвь. Во втором разделе перечислены задания, выполняемые после запуска действия. Действие извлекает соответствующие файлы и обновляет экземпляр конфигурации приложения, используя строку подключения, сохраненную в качестве секрета в репозитории.  Дополнительные сведения об использовании секретов в GitHub см. в [этой статье](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) о создании и использовании зашифрованных секретов.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Использовать динамическую метку при синхронизации
Предыдущее действие просто обновило экземпляр конфигурации приложения всякий раз, когда `appsettings.json` обновляется. Это действие вставляет динамическую метку при каждой синхронизации, гарантируя, что каждая синхронизация может быть однозначно идентифицирована.  Это позволяет быстро сопоставлять изменения кода с изменениями конфигурации.

В первом разделе этого рабочего процесса указывается, что действие активируется *при* *отправке* , содержащем `appsettings.json` в *главную* ветвь. Во втором разделе выполняется задание, которое создает уникальную метку для обновления конфигурации на основе хэша фиксации. Затем задание обновляет экземпляр конфигурации приложения новыми значениями и уникальной меткой для этого обновления.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Использовать жесткое синхронизацию
Если включен строгой режим, синхронизация гарантирует, что экземпляр конфигурации приложения будет точно соответствовать файлу конфигурации для заданного префикса и метки. Пары "ключ-значение" с тем же префиксом и меткой, которые не находятся в файле конфигурации, удаляются. 
 
Если режим «исключительно» не включен, то при синхронизации будут заданы только значения ключа из файла конфигурации. Пары "ключ-значение" не будут удалены. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Использовать максимальную глубину для ограничения действия GitHub
Поведение по умолчанию для вложенных атрибутов JSON состоит в том, чтобы свести весь объект к виду.  Приведенный ниже код JSON определяет эту пару "ключ-значение":

| Ключ | Значение |
| --- | --- |
| Объект: внутренний: Иннеркэй | иннервалуе |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
Если вложенный объект предназначен для использования в качестве значения, переданного в экземпляр конфигурации, можно использовать значение *глубины* , чтобы прерывать сведение на соответствующей глубине. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Учитывая глубину 2, приведенный выше пример теперь возвращает следующую пару "ключ: значение":

| Ключ | Значение |
| --- | --- |
| Объект: внутренний | {"Иннеркэй": "Иннервалуе"} |

## <a name="understand-action-inputs"></a>Общие сведения о входных данных действия
Входные параметры указывают данные, используемые действием во время выполнения.  В следующей таблице содержатся входные параметры, принятые при синхронизации конфигурации приложения, и ожидаемые значения для каждого из них.  Дополнительные сведения о вводе действий для действий GitHub см. в [документации](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)GitHub.

> [!Note]
> Идентификаторы входных данных не чувствительны к регистру.


| Ввод имени | Обязательно? | Значение |
|----|----|----|
| Файл ConfigurationFile | Да | Путь к файлу конфигурации в репозитории относительно корня репозитория.  Шаблоны стандартная маска поддерживаются и могут включать несколько файлов. |
| format | Да | Формат файла конфигурации.  Допустимые форматы: JSON, YAML, свойства. |
| connectionString | Да | Строка подключения для экземпляра конфигурации приложения. Строка подключения должна храниться в качестве секрета в репозитории GitHub, а в рабочем процессе следует использовать только имя секрета. |
| separator | Да | Разделитель, используемый при сведении файла конфигурации с парами "ключ-значение".  Допустимые значения:. , ; : - _ __ / |
| prefix | Нет | Префикс, добавляемый в начало ключей. |
| label | Нет | Метка, используемая при настройке пар "ключ-значение". Если значение не указано, используется метка null. |
| strict | Нет | Логическое значение, определяющее, включен ли режим "в режиме". Значение по умолчанию — false. |
| Длина | Нет | Максимальная глубина для плоской обработки файла конфигурации.  Значение глубины должно быть положительным числом.  Значение по умолчанию не будет превышать максимальную глубину. |
| tags | Нет | Указывает набор тегов для пар "ключ-значение".  Ожидаемый формат является переведенные формой объекта JSON следующей фигуры: {[propertyName: String]: строка;} Каждое свойство name-value преобразуется в тег. |

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о действии "Синхронизация конфигурации приложения" и о том, как его можно использовать для автоматизации обновлений экземпляра конфигурации приложения. Чтобы узнать, как конфигурация приложения Azure реагирует на изменения в парах "ключ-значение", перейдите к следующей [статье](./concept-app-configuration-event.md).
