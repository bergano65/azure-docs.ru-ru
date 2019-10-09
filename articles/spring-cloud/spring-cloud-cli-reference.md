---
title: AZ пружины Cloud
description: Управление пружинным облаком Azure с помощью Azure CLI
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: c0694bf53f4a0644c8da2b50660dbfd6a5b339c7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038864"
---
# <a name="az-spring-cloud"></a>AZ пружины — облако

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Управление пружинным облаком Azure с помощью Azure CLI

>[!Note]
> В настоящее время предварительная версия облачного облака Azure находится в режиме предварительного просмотра.  Эти команды могут быть изменены или удалены в будущем выпуске.

| AZ пружины — облако |  |
|------|------:|
| [AZ пружины — создание облака](#az-spring-cloud-create) | Создайте экземпляр облака Azure весны. |
| [AZ пружина — удаление облака](#az-spring-cloud-delete) | Удаление экземпляра облака Azure весны. |
| [AZ пружины — список облачных сред](#az-spring-cloud-list) | Вывод списка всех экземпляров Azure Веснного облака в заданной группе ресурсов; в противном случае перечислите идентификаторы подписок. |
| [AZ пружины — облачное отображение](#az-spring-cloud-show) | Отображение сведений для Azure Веснного облака. |

| AZ пружины — облачное приложение | Команды для управления приложениями в облаке Azure весны.  |
| ---- | ----: |
| [AZ пружина — облачное приложение создание](#az-spring-cloud-app-create) | Создание нового приложения с развертыванием по умолчанию в Azure Веснного облака. |
| [AZ пружины — удаление облачных приложений](#az-spring-cloud-app-delete) | Удалите приложение в Azure Веснного облака. |
| [AZ пружина — облачное развертывание приложений](#az-spring-cloud-app-deploy) | Развертывание из исходного кода или предварительно созданного двоичного файла в приложение и обновление связанных конфигураций. |
| [AZ пружины — список облачных приложений](#az-spring-cloud-app-list) | Перечислите все приложения в Azure Веснного облака. |
| [AZ пружина — перезапуск облачного приложения](#az-spring-cloud-app-restart) | Перезапустите экземпляры приложения, используя значения по умолчанию для развертывания в рабочей среде. |
| [AZ пружины — масштабирование облачных приложений](#az-spring-cloud-app-scale) | Ручное масштабирование приложения или его развертываний. |
| [AZ пружины — набор облачных приложений — развертывание](#az-spring-cloud-app-set-deployment) | Задание рабочего развертывания приложения. |
| [AZ пружина — демонстрация облачных приложений](#az-spring-cloud-app-show) | Отображение сведений о приложении в Azure Веснного облака. |
| [AZ пружины — Cloud App, демонстрация-Deploy — журнал](#az-spring-cloud-app-show-deploy-log) | Отображение журналов сборки для последнего развертывания из источника. По умолчанию используется рабочее развертывание. |
| [AZ пружина — Запуск облачного приложения](#az-spring-cloud-app-start) | Запуск экземпляров приложения с использованием значений по умолчанию для рабочего развертывания. |
| [AZ пружина — завершение облачного приложения](#az-spring-cloud-app-stop) | Останавливает экземпляры приложения, используя значения по умолчанию для развертывания в рабочей среде. |
| [AZ пружина — обновление облачного приложения](#az-spring-cloud-app-update) | Обновление конфигурации указанного приложения. |

| AZ пружина — привязка облачных приложений | Команды для управления привязками со службами данных Azure.  Чтобы эти параметры вступили в силу, необходимо перезапустить приложение. |
| --- | ---: |
| [AZ пружины — список привязок облачных приложений](#az-spring-cloud-app-binding-list) | Перечисление всех привязок службы в приложении. |
| [AZ пружина — удаление привязки облачного приложения](#az-spring-cloud-app-binding-remove) | Удаляет привязку службы из приложения. |
| [AZ пружины — отображение привязки облачных приложений](#az-spring-cloud-app-binding-show) | Отображение сведений о привязке службы. |
| [AZ пружина — Cosmos Добавление привязки облачного приложения](#az-spring-cloud-app-binding-cosmos-add) | Привяжите CosmosDB Azure к приложению. |
| [AZ пружины — Cosmos обновление привязки облачного приложения](#az-spring-cloud-app-binding-cosmos-update) | Обновите привязку службы CosmosDB Azure. |
| [AZ пружина — привязка облачных приложений MySQL добавить](#az-spring-cloud-app-binding-mysql-add) | Привяжите базу данных Azure для MySQL к приложению. |
| [AZ пружина — обновление привязки облачного приложения MySQL](#az-spring-cloud-app-binding-mysql-update) | Обновите привязку службы "база данных Azure для MySQL". |
| [AZ пружина — Redis Добавление привязки облачного приложения](#az-spring-cloud-app-binding-redis-add) | Привяжите кэш Azure для Redis к приложению. |
| [AZ пружины — Redis обновление привязки облачного приложения](#az-spring-cloud-app-binding-redis-update) | Обновите кэш Azure для привязки службы Redis. |

| AZ пружины — развертывание облачных приложений | Команды для управления жизненным циклом развертывания приложения в Azure Веснного облака. |
| --- | ---: |
| [AZ пружина — развертывание облачных приложений создание](#az-spring-cloud-app-deployment-create) | Создайте промежуточное развертывание для приложения. |
| [AZ пружина — развертывание облачного приложения Удаление](#az-spring-cloud-app-deployment-delete) | Удаление развертывания приложения. |
| [AZ пружины — список развертывания облачных приложений](#az-spring-cloud-app-deployment-list) | Вывод списка всех развертываний в приложении. |
| [AZ пружины — демонстрация развертывания облачных приложений](#az-spring-cloud-app-deployment-show) | Отображение сведений о развертывании. |

| AZ пружины — облачная Конфигурация — сервер | Команды для управления сервером облачной конфигурации Azure весны. |
| --- | ---: |
| [AZ пружины — облачная конфигурация — очистка сервера](#az-spring-cloud-config-server-clear) | Удаление всех параметров на сервере конфигурации. |
| [AZ пружины — облачная конфигурация — набор серверов](#az-spring-cloud-config-server-set) | Определите сервер конфигурации из файла YAML. |
| [AZ пружины — облачная конфигурация — демонстрация серверов](#az-spring-cloud-config-server-show) | Отображение конфигурации сервера конфигурации. |
| [AZ пружина — набор Git облачной конфигурации сервера](#az-spring-cloud-config-server-git-set) | Определите свойства Git для сервера конфигурации.  Предыдущие значения будут перезаписаны. |
| [AZ пружины — Добавление репозитория Git для сервера настройки облака](#az-spring-cloud-config-server-git-repo-add) | Добавьте новую конфигурацию репозитория Git на сервер конфигурации. |
| [AZ пружины — список репозитория Git для сервера конфигурации облака](#az-spring-cloud-config-server-git-repo-list) | Список всех конфигураций репозитория Git для сервера конфигурации. |
| [AZ пружины — Удаление репозитория Git сервера настройки облака](#az-spring-cloud-config-server-git-repo-remove) | Удалите указанный репозиторий Git с сервера конфигурации. |

| AZ пружины — облачный тест — конечная точка | Команды для управления тестированием конечных точек в Azure Веснного облака |
| --- | ---: |
| [AZ пружина — тестирование облака — отключение конечной точки](#az-spring-cloud-test-endpoint-disable) | Отключите тестовую конечную точку. |
| [AZ пружины — облачный тест — конечная точка — включить](#az-spring-cloud-test-endpoint-enable) | Включить конечную точку теста. |
| [AZ пружины — облачный тест — список конечных точек](#az-spring-cloud-test-endpoint-list) | Вывод списка ключей тестовой конечной точки. |
| [AZ пружина — облачный тест — конечная точка продление — ключ](#az-spring-cloud-test-endpoint-renew-key) | Повторное создание ключа тестовой конечной точки. |

## <a name="az-spring-cloud-create"></a>AZ пружины — создание облака

Создание нового приложения с развертыванием по умолчанию в Azure Веснного облака.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя для этого экземпляра облака Azure весны. |
| --resource-group -g | Указывает группу ресурсов для этого приложения.  Настройка группы по умолчанию с помощью `az configure --defaults group=<name>` |

| Необязательные параметры | |
| --- | ---: |
| --location -l | Указывает расположение сервера для этого приложения.  Поиск допустимых расположений с помощью `az account list-locations` |
| --no-wait | Не следует выполнять длительные операции.

### <a name="examples"></a>Примеры

Создание нового Azure Веснного облака в WestUS

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ пружина — удаление облака

Удаление экземпляра облака Azure весны.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя удаляемого экземпляра Azure весны. |
| --resource-group -g | Имя группы ресурсов, к которой принадлежит Azure Веснного облака. |

| Необязательные параметры | |
| --- | ---: |
| -нет-ожидание | Не дожидаться завершения долго выполняемых операций. |

### <a name="example"></a>Пример

Удалить экземпляр облачной службы Azure весны с именем "MyService" из "MyResourceGroup".

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ пружины — список облачных сред

Список всех облачных экземпляров Azure весны, связанных с заданной группой ресурсов. Если группа ресурсов не указана, выведите список идентификаторов подписок.

```cli
az spring-cloud list --resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --resource-group -g | Имя группы ресурсов. |

## <a name="az-spring-cloud-show"></a>AZ пружины — облачное отображение

Отображение сведений об указанном экземпляре Azure весны облака.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя экземпляра Azure весны облака. |
| --resource-group -g | Имя группы ресурсов, к которой принадлежит экземпляр облака Azure весны.

## <a name="az-spring-cloud-app-create"></a>AZ пружина — облачное приложение создание

Создание нового приложения в Azure Веснного облака.

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --ЦП | Число виртуальных ядер на экземпляр.  Значение по умолчанию: 1. |
| --Enable-persistent-хранилище | Логическое значение.  При значении true подключает диск 50 ГБ с путем по умолчанию. |
| --instance-count | Число экземпляров.  Значение по умолчанию: 1. |
| --является открытым | Логическое значение.  Если значение — true, назначает общедоступный домен. |
| --память | Количество ГБ памяти на экземпляр.  Значение по умолчанию: 1. |

### <a name="examples"></a>Примеры

Создайте приложение с конфигурацией по умолчанию.

```cli
az spring-cloud app create -n MyApp -s MyService
```

Создайте общедоступное приложение с тремя экземплярами.  У каждого экземпляра есть 3 ГБ памяти и 2 ядра ЦП.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ пружины — удаление облачных приложений

Удаляет приложение в Azure Веснного облака.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ пружина — облачное развертывание приложений

Развертывание приложения в Azure Веснного облака из исходного кода или предварительно созданного двоичного файла и обновление связанных конфигураций.

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --ЦП | Число виртуальных процессоров CPI на экземпляр. |
| --Deployment-d | Имя существующего развертывания приложения.  Если значение не указано, по умолчанию используется рабочее развертывание. |
| --env | Переменные окружения, разделенные пробелами, в формате "ключ [= значение]". |
| --instance-count | Число экземпляров. |
| --JAR-Path | Если указано, разверните JAR-файл по указанному пути. В противном случае разверните текущую папку как tar. |
| --виртуальной машины Java-параметры | Строка, содержащая параметры ВИРТУАЛЬНОЙ машины Java.  Используйте "=" вместо "", чтобы избежать ошибок синтаксического анализа оболочки. Например, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --память | Количество ГБ памяти на экземпляр. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |
| --Runtime-Version | Версия среды выполнения языка, используемого в приложении.  Допустимые значения: `Java_11`, `Java_8`. |
| --Target-Module | Дочерний модуль для развертывания.  Требуется при построении нескольких пакетов jar из исходного кода. |
| --version | Версия развертывания.  Без изменений, если не задано. |

### <a name="examples"></a>Примеры

Развертывание исходного кода в приложении. Это приведет к упаковке текущего каталога, созданию двоичного файла с помощью службы сводных сборок, а затем к развертыванию в приложении.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

Развертывание предварительно созданного JAR-файла в приложении с помощью параметров ВИРТУАЛЬНОЙ машины Java и переменных среды.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Развертывание исходного кода в определенном развертывании приложения.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ пружины — список облачных приложений

Список всех приложений в облачном экземпляре Azure весны.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Необходимые параметры | |
| --- | ---: |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ пружина — перезапуск облачного приложения

Перезапустите экземпляры приложения.  По умолчанию используется рабочее развертывание.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения.  Если значение не указано, по умолчанию используется рабочее развертывание. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |

## <a name="az-spring-cloud-app-scale"></a>AZ пружины — масштабирование облачных приложений

Ручное масштабирование приложения или его развертываний.

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --ЦП | Число виртуальных ядер ЦП на экземпляр приложения. |
| --Deployment-d | Имя существующего развертывания приложения.  Если значение не указано, по умолчанию используется рабочее развертывание. |
| --instance-count | Число экземпляров этого приложения. |
| --память | Количество ГБ памяти для каждого экземпляра приложения. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |

### <a name="examples"></a>Примеры

Увеличьте масштаб приложения до 4 ядер ЦП и 8 ГБ памяти на экземпляр.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Масштабирование развертывания приложения до 5 экземпляров.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ пружины — набор облачных приложений — развертывание

Задайте параметры конфигурации для рабочего развертывания приложения.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения. |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |

### <a name="examples"></a>Примеры

Переключить промежуточное развертывание приложения в рабочую среду.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ пружина — демонстрация облачных приложений

Отображение сведений о приложении в Azure Веснного облака.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ пружины — Cloud App, демонстрация-Deploy — журнал

Отображение журнала сборки последнего развертывания из исходного кода.  По умолчанию используется рабочая среда.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения.  По умолчанию используется рабочая среда. |

## <a name="az-spring-cloud-app-start"></a>AZ пружина — Запуск облачного приложения

Запускает экземпляры приложения.  По умолчанию используется рабочая среда.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения.  По умолчанию используется рабочая среда. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |

## <a name="az-spring-cloud-app-stop"></a>AZ пружина — завершение облачного приложения

Останавливает экземпляры приложения.  По умолчанию используется рабочая среда.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения.  По умолчанию используется рабочая среда. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |

## <a name="az-spring-cloud-app-update"></a>AZ пружина — обновление облачного приложения

Обновите сохраненную конфигурацию приложения.

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Необходимые параметры | |
| --- | ---: |
| --Name-n | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Deployment-d | Имя существующего развертывания приложения.  По умолчанию используется рабочая среда. |
| --Enable-persistent-хранилище | Логическое значение.  Если значение — true, подключите диск 50 ГБ с путем по умолчанию. |
| --env | Переменные окружения, разделенные пробелами, в формате "ключ [= значение]". |
| --является открытым | Логическое значение.  Если значение — true, назначьте приложению общедоступный домен. |
| --виртуальной машины Java-параметры | Строка, содержащая параметры ВИРТУАЛЬНОЙ машины Java.  Используйте "=" вместо "", чтобы избежать ошибок синтаксического анализа оболочки. Например, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |
| --Runtime-Version | Версия среды выполнения языка, используемого в приложении.  Допустимые значения: `Java_11`, `Java_8`. |

### <a name="example"></a>Пример

Добавьте переменную среды для приложения.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ пружины — список привязок облачных приложений

Перечисление всех привязок службы в приложении.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ пружина — удаление привязки облачного приложения

Удаляет привязку службы из приложения.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя удаляемой привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ пружины — отображение привязки облачных приложений

Отображение сведений о привязке службы.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ пружина — Cosmos Добавление привязки облачного приложения

Привяжите Azure Cosmos DB к приложению.

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --API-тип | Укажите тип API, используя одно из следующих значений: Cassandra, Gremlin, Mongo, SQL, Table. |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

|Необязательные параметры | |
| --- | ---: |
| --Collection-Name | Имя коллекции.  Требуется при использовании Gremlin. |
| --имя базы данных | Имя базы данных.  Требуется при использовании Mongo, SQL и Gremlin. |
| --ключ — пространство | Cassandra Key — пространство.  Требуется при использовании Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ пружины — Cosmos обновление привязки облачного приложения

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

|Необязательные параметры | |
| --- | ---: |
| --Collection-Name | Имя коллекции.  Требуется при использовании Gremlin. |
| --имя базы данных | Имя базы данных.  Требуется при использовании Mongo, SQL и Gremlin. |
| --ключ — пространство | Cassandra Key — пространство.  Требуется при использовании Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ пружина — привязка облачных приложений MySQL добавить

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --имя базы данных | Имя базы данных. |
| --key | Ключ API службы. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Resource-ID | Идентификатор ресурса Azure для привязки к службе. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |
| --username | Имя пользователя для доступа к базе данных. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ пружина — обновление привязки облачного приложения MySQL

Обновление привязки службы подключение приложения к базе данных Azure для MySQL.

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --имя базы данных | Имя базы данных. |
| --key | Ключ API службы. |
| --username | Имя пользователя для доступа к базе данных. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ пружина — Redis Добавление привязки облачного приложения

Привяжите кэш Azure для Redis к приложению.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Resource-ID | Идентификатор ресурса Azure службы, с которой необходимо выполнить привязку. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Disable-SSL | Отключите SSL. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ пружины — Redis обновление привязки облачного приложения

Обновите привязку службы для кэша Azure для Redis.

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --Disable-SSL | Отключите SSL. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ пружина — развертывание облачных приложений создание

Создайте промежуточное развертывание для приложения.

Чтобы развернуть код или обновить параметры существующего развертывания, используйте `az spring-cloud app deploy --deployment <staging-deployment>` или AZ пружинное обновление облачного приложения — развертывание <staging deployment>.

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя привязки службы. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --ЦП | Число виртуальных ядер ЦП на экземпляр.  Значение по умолчанию: 1 |
| --env | Переменные окружения, разделенные пробелами, в формате "ключ [= значение]". |
| --instance-count | Число экземпляров. Значение по умолчанию: 1. |
| --JAR-Path | Если оно предоставлено, разверните JAR-файл.  В противном случае разверните текущую папку как tar. |
| --виртуальной машины Java-параметры | Строка, содержащая параметры ВИРТУАЛЬНОЙ машины Java.  Используйте "=" вместо "", чтобы избежать ошибок синтаксического анализа оболочки. Например, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --память | Количество ГБ памяти на экземпляр. |
| --no-wait | Не дожидаться завершения долго выполняемых операций. |
| --Runtime-Version | Версия среды выполнения языка, используемого в приложении.  Допустимые значения: `Java_11`, `Java_8`. |
| --Skip-Clone-Settings | Создайте промежуточное развертывание, клонировать текущие параметры развертывания в рабочей среде. |
| --Target-Module | Дочерний модуль для развертывания.  Требуется при построении нескольких пакетов jar из исходного кода. |
| --version | Версия развертывания.  Без изменений, если не задано. |

### <a name="examples"></a>Примеры

Развертывание исходного кода в новом развертывании приложения.  Это приведет к упаковке текущего каталога, созданию двоичного файла с помощью системы сводных построений, а затем развертыванию.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Развертывание предварительно созданного JAR-файла в приложении с помощью параметров ВИРТУАЛЬНОЙ машины Java и переменных среды.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ пружина — развертывание облачного приложения Удаление

Удаление развертывания приложения.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя развертывания. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ пружины — список развертывания облачных приложений

Вывод списка всех развертываний в приложении.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ пружины — демонстрация развертывания облачных приложений

Отображение сведений о развертывании.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Необходимые параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --name | Имя развертывания. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Service-s | Имя облака Azure весны.  Службу по умолчанию можно настроить с помощью `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ пружины — облачная конфигурация — очистка сервера

Удаление всех параметров конфигурации на сервере конфигурации.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ пружины — облачная конфигурация — набор серверов

Установите параметры конфигурации на сервере конфигурации с помощью файла YAML.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Необходимые параметры | |
| --- | ---: |
| --config-файл | Путь к файлу манифеста YAML для конфигурации сервера конфигурации. |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --no-wait | Не следует выполнять длительные операции.

## <a name="az-spring-cloud-config-server-show"></a>AZ пружины — облачная конфигурация — демонстрация серверов

Отображение параметров сервера конфигурации.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ пружины — облачная конфигурация — набор Git сервера

Задайте свойства Git для сервера конфигурации.  Все существующие свойства Git будут перезаписаны.

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --URI | URI добавленной конфигурации. |

| Необязательные параметры | |
| --- | ---: |
| --отсрочка | Временно Сохраните объект в локальном кэше вместо отправки в Azure.  Для просмотра/очистки используйте `az cache`. |
| --узел-ключ | Ключ узла для добавленной конфигурации. |
| --Host-Key-Algorithm | Алгоритм ключа узла для добавленной конфигурации. |
| --Метка | Метка добавленной конфигурации. |
| --password | Пароль для добавленной конфигурации. |
| --закрытый ключ | Закрытый ключ добавленной конфигурации. |
| --Поиск-пути | Пути поиска добавленной конфигурации.  Используйте разделители запятые для нескольких путей. |
| --ограничивало-ключ-проверка | Включает проверку соответствия ключа узла для добавленной конфигурации. |
| --username | Имя пользователя для добавленной конфигурации. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ пружины — облачная конфигурация — Добавление репозитория Git сервера

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --репозиторий-имя | URI репозитория. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --URI | URI добавленной конфигурации. |

| Необязательные параметры | |
| --- | ---: |
| --отсрочка | Временно Сохраните объект в локальном кэше вместо отправки в Azure.  Для просмотра/очистки используйте `az cache`. |
| --узел-ключ | Ключ узла для добавленной конфигурации. |
| --Host-Key-Algorithm | Алгоритм ключа узла для добавленной конфигурации. |
| --Метка | Метка добавленной конфигурации. |
| --password | Пароль для добавленной конфигурации. |
| --шаблон | Шаблон для репозитория.  Используйте разделители запятые для нескольких путей.|
| --закрытый ключ | Закрытый ключ добавленной конфигурации. |
| --Поиск-пути | Пути поиска добавленной конфигурации.  Используйте разделители запятые для нескольких путей. |
| --ограничивало-ключ-проверка | Включает проверку соответствия ключа узла для добавленной конфигурации. |
| --username | Имя пользователя для добавленной конфигурации. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ пружины — облачная конфигурация — список репозитория Git сервера

Список всех репозиториев Git, определенных на сервере конфигурации

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --отсрочка | Временно Сохраните объект в локальном кэше вместо отправки в Azure.  Для просмотра/очистки используйте `az cache`. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ пружины — облачная конфигурация — Удаление репозитория Git сервера

Удалите существующую конфигурацию репозитория Git с сервера конфигурации.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --репозиторий-имя | URI репозитория. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --отсрочка | Временно Сохраните объект в локальном кэше вместо отправки в Azure.  Для просмотра/очистки используйте `az cache`. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ пружина — тестирование облака — отключение конечной точки

Отключение тестовой конечной точки для облака Azure весны

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ пружины — облачный тест — конечная точка — включить

Включите конечную точку теста для Azure Веснного облака. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ пружины — облачный тест — список конечных точек 

Список доступных ключей тестовых конечных точек для Azure Веснного облака.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |

| Необязательные параметры | |
| --- | ---: |
| --приложение | Имя приложения. |
| --Deployment-d | Имя существующего развертывания приложения.  Если не указано, по умолчанию используется значение Production. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ пружина — облачный тест — конечная точка продление — ключ

Повторное создание ключа тестовой конечной точки для Azure Веснного облака.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Необходимые параметры | |
| --- | ---: |
| --name | Имя облака Azure весны. |
| --resource-group -g | Имя группы ресурсов.  Вы можете настроить расположение по умолчанию с помощью `az configure --defaults group=<name>`. |
| --Тип | Тип ключа тестовой конечной точки.  Допустимые значения:  Первичный, вторичный. |
