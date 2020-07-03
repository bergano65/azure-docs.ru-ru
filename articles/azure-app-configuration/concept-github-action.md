---
title: Синхронизация репозитория GitHub с конфигурацией приложения
description: Используйте действия GitHub для автоматического обновления экземпляра конфигурации приложения при обновлении репозитория GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585475"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Синхронизация репозитория GitHub с конфигурацией приложения

Команды, которые хотят продолжать использовать существующие методы управления версиями, могут использовать действия GitHub для автоматической синхронизации своего репозитория GitHub с хранилищем конфигурации приложений. Это позволяет вносить изменения в файлы конфигурации обычным образом, в то же время получая такие преимущества конфигурации приложений, как: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Централизованная настройка вне кода <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Обновление конфигурации без повторного развертывания всего приложения <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Интеграция со службами, такими как служба приложений Azure и функции. 

[Рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) GitHub Actions определяет автоматизированный процесс в репозитории GitHub. Действие *Синхронизация конфигурации приложения Azure* активирует обновление экземпляра конфигурации приложения при внесении изменений в исходный репозиторий. Он использует файл YAML (. yml), найденный в `/.github/workflows/` пути к репозиторию, чтобы определить шаги и параметры. Вы можете активировать обновления конфигурации при отправке, просмотре или ветвлении файлов конфигурации приложения так же, как и с кодом приложения.

[Документация](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) по GitHub содержит подробные сведения о рабочих процессах и действиях GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Включение действий GitHub в репозитории
Чтобы начать работу с этим действием GitHub, перейдите в репозиторий и перейдите на вкладку " **действия** ". Щелкните " **создать рабочий процесс**", а затем **Настройте рабочий процесс самостоятельно**. Наконец, выполните поиск по запросу "Синхронизация конфигурации приложений Azure" в Marketplace.
> [!div class="mx-imgBorder"]
> ![Выбор вкладки "действие"](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Выберите действие синхронизации конфигурации приложения](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Синхронизация файлов конфигурации после отправки
Это действие синхронизирует файлы конфигурации приложений Azure при отправке изменений в `appsettings.json`. Когда разработчик отправляет изменение в `appsettings.json`, действие синхронизации конфигурации приложения обновляет экземпляр конфигурации приложения новыми значениями.

В первом разделе этого рабочего процесса указывается, что действие активируется *при* *отправке* , содержащемся `appsettings.json` в *главную* ветвь. Во втором разделе перечислены задания, выполняемые после запуска действия. Действие извлекает соответствующие файлы и обновляет экземпляр конфигурации приложения, используя строку подключения, сохраненную в качестве секрета в репозитории.  Дополнительные сведения об использовании секретов в GitHub см. в [статье GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) о создании и использовании зашифрованных секретов.

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
Предыдущее действие обновляет экземпляр конфигурации приложения всякий `appsettings.json` раз, когда обновляется. Это действие вставляет динамическую метку при каждой синхронизации, гарантируя, что каждая синхронизация может быть однозначно идентифицирована и позволяет сопоставлять изменения кода с изменениями конфигурации.

В первом разделе этого рабочего процесса указывается, что действие активируется *при* *отправке* , содержащемся `appsettings.json` в *главную* ветвь. Во втором разделе выполняется задание, которое создает уникальную метку для обновления конфигурации на основе хэша фиксации. Затем задание обновляет экземпляр конфигурации приложения новыми значениями и уникальной меткой для этого обновления.

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
Если включен строгой режим, синхронизация гарантирует, что экземпляр конфигурации приложения будет точно соответствовать файлу конфигурации для заданного префикса и метки. Пары "ключ-значение" с тем же префиксом и меткой, которые отсутствуют в файле конфигурации, удаляются. 
 
Если этот режим не включен, то при синхронизации будут заданы только значения ключа из файла конфигурации. Пары "ключ-значение" не будут удалены. 

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

| Клавиши | Значение |
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

Учитывая глубину 2, приведенный выше пример теперь возвращает следующую пару "ключ-значение":

| Клавиши | Значение |
| --- | --- |
| Объект: внутренний | {"Иннеркэй": "Иннервалуе"} |

## <a name="understand-action-inputs"></a>Общие сведения о входных данных действия
Входные параметры указывают данные, используемые действием во время выполнения.  В следующей таблице содержатся входные параметры, принятые при синхронизации конфигурации приложения, и ожидаемые значения для каждого из них.  Дополнительные сведения о вводе действий для действий GitHub см. в [документации](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)GitHub.

> [!Note]
> Идентификаторы входных данных не чувствительны к регистру.


| Ввод имени | Необходим? | Значение |
|----|----|----|
| Файл ConfigurationFile | Да | Относительный путь к файлу конфигурации в репозитории.  Шаблоны стандартная маска поддерживаются и могут включать несколько файлов. |
| format | Да | Формат файла конфигурации.  Допустимые форматы: JSON, YAML, свойства. |
| connectionString | Да | Строка подключения для экземпляра конфигурации приложения. Строка подключения должна храниться в качестве секрета в репозитории GitHub, а в рабочем процессе следует использовать только имя секрета. |
| separator | Да | Разделитель, используемый при сведении файла конфигурации с парами "ключ-значение".  Допустимые значения:. , ; : - _ __ / |
| prefix | Нет | Префикс, добавляемый в начало ключей. |
| label | Нет | Метка, используемая при настройке пар "ключ-значение". Если значение не указано, используется метка null. |
| strict | Нет | Логическое значение, определяющее, включен ли режим "в режиме". Значение по умолчанию — false. |
| Длина | Нет | Максимальная глубина для плоской обработки файла конфигурации.  Значение глубины должно быть положительным числом.  Значение по умолчанию не будет превышать максимальную глубину. |
| tags | Нет | Указывает набор тегов для пар "ключ-значение".  Ожидаемый формат является переведенные формой объекта JSON следующей фигуры: {[propertyName: String]: строка;} Каждое свойство name-value преобразуется в тег. |

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы узнали о действии "Синхронизация конфигурации приложения" и о том, как его можно использовать для автоматизации обновлений экземпляра конфигурации приложения. Чтобы узнать, как конфигурация приложения Azure реагирует на изменения в парах "ключ-значение", перейдите к следующей [статье](./concept-app-configuration-event.md).
