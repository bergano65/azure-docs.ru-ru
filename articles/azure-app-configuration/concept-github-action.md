---
title: Используйте действия GitHub с синхронизацией конфигурации приложений Azure
description: Используйте действия GitHub для запуска обновления в экземпляре конфигурации приложения при обновлении репозитория GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46d4aa4d4d37e9cac928e8d1a9e5e77ca0f30f18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384064"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Синхронизация экземпляра службы "Конфигурация приложений" с помощью действий GitHub

Команды, которые хотят продолжать использовать существующие методы управления исходными данными, могут использовать действия GitHub для автоматической синхронизации репозитория GitHub с магазином конфигурации приложений. Это позволяет вносить изменения в файлы конфигурации, как обычно, получая при этом преимущества конфигурации приложений, такие как: <br>
&nbsp;&nbsp;&nbsp;&nbsp;Централизованная конфигурация вне кода <br>
&nbsp;&nbsp;&nbsp;&nbsp;Обновление конфигурации без передислокации всего приложения <br>
&nbsp;&nbsp;&nbsp;&nbsp;Интеграция с такими сервисами, как Служба приложений Azure и функции. 

[Рабочий процесс](https://help.github.com/articles/about-github-actions#workflow) действий GitHub определяет автоматизированный процесс в репозитории GitHub. Действие *azure App Configuration Sync* запускает обновления в экземпляре конфигурации приложения при внесении изменений в исходное репозиторий. Он использует файл YAML (.yml), `/.github/workflows/` найденный в пути репозитория, для определения шагов и параметров. Вы можете инициировать обновления конфигурации при нажатии, просмотре или ветвления файлах конфигурации приложений так же, как и в случае с кодом приложения.

[Документация](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHub предоставляет углубленное представление рабочих процессов и действий GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Включить действия GitHub в репозитории
Чтобы начать использовать это действие GitHub, перейдите в репозиторий и выберите вкладку **«Действия.** Нажмите на **новый рабочий процесс»,** затем **настройте рабочий процесс самостоятельно.** Наконец, ищите на рынке "Azure App Configuration Sync".
> [!div class="mx-imgBorder"]
> ![Выберите вкладку «Действие»](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Выберите синхронизацию конфигурации приложения Action](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Синхронизация файлов конфигурации после нажатия
Это действие синхронизирует файлы конфигурации приложений `appsettings.json`Azure при перемещении в . Когда разработчик нажимает `appsettings.json`на изменение, действие App Configuration Sync обновляет экземпляр конфигурации приложения с новыми значениями.

В первом разделе рабочего процесса указывается, что действие вызывает `appsettings.json` *нажатие,* содержащееся в *основной* ветви. *on* Во втором разделе перечислены задания, запущенные после запуска действия. Действие проверяет соответствующие файлы и обновляет экземпляр конфигурации Приложения, используя строку соединения, хранящуюся в качестве секрета в репозитории.  Более подробную информацию об использовании секретов можно узнать в GitHub, смотрите [статью GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) о создании и использовании зашифрованных секретов.

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

## <a name="use-a-dynamic-label-on-sync"></a>Используйте динамическую метку на синхронизации
Предыдущее действие обновляет экземпляр конфигурации приложения всякий раз, `appsettings.json` когда обновляется. Это действие вставляет динамическую метку на каждую синхронизацию, гарантируя, что каждая синхронизация может быть однозначно идентифицирована и позволяя отображать изменения кода для изменения конфигурации.

В первом разделе рабочего процесса указывается, что действие вызывает `appsettings.json` *нажатие,* содержащееся в *основной* ветви. *on* Во втором разделе выполняется задание, которое создает уникальную метку для обновления конфигурации на основе хэша коммита. Затем задание обновляет экземпляр конфигурации приложения с новыми значениями и уникальной меткой для этого обновления.

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

## <a name="use-strict-sync"></a>Используйте строгую синхронизацию
При включении строгого режима синхронизация гарантирует, что экземпляр конфигурации приложения точно соответствует файлу конфигурации для данной префикса и метки. Пары ключей с той же приставкой и меткой, которых нет в файле конфигурации, удаляются. 
 
Если строгий режим не включен, синхронизация устанавливает только значения ключей из файла конфигурации. Пары с ключевым значением не будут удалены. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Используйте максимальную глубину, чтобы ограничить действия GitHub
Поведение по умолчанию для вложенных атрибутов JSON заключается в том, чтобы сгладить весь объект.  Ниже JSON определяет эту ключевую стоимость пары:

| Ключ | Значение |
| --- | --- |
| Объект:Внутренний:Внутреннийключ | Внутренняястоимость |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Если вложенный объект предназначен для значения, отодвинутого на пример Конфигурации, можно использовать значение *глубины,* чтобы остановить уплощение на соответствующей глубине. 

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

Учитывая глубину 2, приведенный выше пример возвращает следующую пару ключевых значений:

| Ключ | Значение |
| --- | --- |
| Объект:Внутренний | «InnerKey":"Внутренняя стоимость" |

## <a name="understand-action-inputs"></a>Понимание входных данных действий
Параметры ввода определяют данные, используемые действием во время выполнения.  Следующая таблица содержит параметры ввода, принятые Синхронизацией конфигурации приложений, и ожидаемые значения для каждого из них.  Для получения дополнительной информации о вводах [действий](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)для действий GitHub см.

> [!Note]
> Иди кто-то из входных данных является нечувствительным.


| Ввод имени | Обязательно? | Значение |
|----|----|----|
| configurationFile | Да | Относительный путь к файлу конфигурации в репозитории.  Шаблоны Glob поддерживаются и могут включать несколько файлов. |
| format | Да | Формат файла файла конфигурации.  Действительные форматы: JSON, YAML, свойства. |
| connectionString | Да | Строка подключения для экземпляра конфигурации приложения. Строка соединения должна храниться как секрет в репозитории GitHub, и в рабочем процессе следует использовать только секретное имя. |
| separator | Да | Сепаратор используется при выравнивании файла конфигурации для пар с ключевым значением.  Действительные значения: . , ; : - _ __ / |
| prefix | нет | Префикс, который будет добавлен к началу ключей. |
| label | нет | Метка используется при настройке пар значения ключа. Если не указано, используется нулевая метка. |
| strict | нет | Значение boolean, определяющее, включен ли строгий режим. Значение по умолчанию — false. |
| Глубина | нет | Максимальная глубина для выравнивания файла конфигурации.  Глубина должна быть положительным числом.  По умолчанию не будет иметь максимальной глубины. |
| tags | нет | Установить тег, установленный на парах значения ключей.  Ожидаемый формат представляет собой струнную форму объекта JSON следующей формы: «СвойствоИмя: строка»: строка; Каждое значение имени свойства становится тегом. |

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о приложении Конфигурация Sync GitHub действий и как он может быть использован для автоматизации обновлений для вашего экземпляра конфигурации приложения. Чтобы узнать, как конфигурация приложений Azure реагирует на изменения в парах ключей, перейти к следующей [статье](./concept-app-configuration-event.md).
