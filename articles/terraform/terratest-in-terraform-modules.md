---
title: Тестирование модулей Terraform в Azure с помощью Terratest
description: Узнайте, как с помощью Terratest тестировать модули Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638717"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Тестирование модулей Terraform в Azure с помощью Terratest

Модули Terraform используются для создания многократно используемых, составных и тестируемых компонентов. Они внедряют инкапсуляцию в сфере инфраструктуры как кода.

Как и в случае с другими компонентами программного обеспечения, контроль качества играет важную роль в модулях Terraform. К сожалению, доступно мало документации, в которой объясняется, как создавать модульные тесты и тесты интеграции в модулях Terraform. В этом руководстве описывается тестирование инфраструктуры и представлены рекомендации, реализованные при создании [модулей Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Рассмотрев все наиболее популярные инфраструктуры тестирования, мы решили использовать [Terratest](https://github.com/gruntwork-io/terratest). Terratest реализуется в виде библиотеки Go. Он предоставляет набор вспомогательных функций и шаблонов для распространенных задач тестирования инфраструктуры, таких как создание HTTP-запросов и подключение к определенной виртуальной машине по протоколу SSH. Ниже приведены некоторые из основных преимуществ Terratest.

- **Он предоставляет удобные вспомогательные методы проверки инфраструктуры.** Эта функция полезна, если вы хотите проверить реальную инфраструктуру в реальной среде.
- **Структура папок хорошо упорядочена.** Тестовые случаи будут хорошо организованы и будут соответствовать [стандартной структуре папок модуля Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Все тестовые случаи написаны на языке Go.** Так как большинство разработчиков Terraform уже являются разработчиками Go, использование Terraform избавляет от необходимости учить еще один язык программирования. Кроме того, единственные зависимости, необходимые для выполнения тестовых случаев в Terratest, — это Go и Terraform.
- **Эта инфраструктура обладает большими возможностями расширения.** Расширить дополнительные функции, например, связанные с Azure, на Terratest не трудно.

## <a name="prerequisites"></a>Предварительные требования

Это практическое руководство не зависит от платформы; инструкции можно выполнять в ОС Windows, Linux или MacOS. Прежде чем продолжить, установите следующее программное обеспечение:

- **Язык программирования Go**. Тестовые случаи Terraform написаны на языке программирования [Go](https://golang.org/dl/).
- **dep**. [dep](https://github.com/golang/dep#installation) — это средство управления зависимостями для Go.
- **Azure CLI**. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) — это программа командной строки для управления ресурсами Azure. (Terraform поддерживает проверку подлинности в Azure с помощью субъекта-службы или [через Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**. Мы используем [исполняемый файл mage](https://github.com/magefile/mage/releases), чтобы узнать, как упростить выполнение вариантов Terratest. 

## <a name="create-a-static-webpage-module"></a>Создание модуля статической веб-страницы

В этом руководстве вы создадите модуль Terraform, который подготовит к работе статическую веб-страницу, передав один HTML-файл в большой двоичный объект службы хранилища Azure. Этот модуль предоставит пользователям по всему миру доступ к этой веб-странице с помощью URL-адреса, возвращаемого модулем.

> [!NOTE]
> Все файлы, описанные в этом разделе, следует создать в разделе [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Сначала создайте папку с именем `staticwebpage` в папке `src` GoPath. Общая структура папок для работы с этим руководством показана ниже. (Файлы, отмеченные звездочкой `(*)`, являются основным приоритетом в этом разделе.)

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Модуль статической веб-страницы принимает три вида входных данных, которые объявлены в `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Как упоминалось ранее, этот модуль также будет выводить URL-адрес, объявленный в `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Это приводит нас к основной логике этого модуля. В целом он подготавливает четыре ресурса:
- Группу ресурсов с именем входных данных `website_name` с добавлением `-staging-rg`.
- Учетную запись хранения с именем входных данных `website_name` с добавлением `data001`. Однако в соответствии с ограничениями по имени учетной записи хранения модуль удалит все специальные символы и запишет имя в нижнем регистре.
- Исправленный именованный контейнер `wwwroot`, созданный в указанной выше учетной записи хранения.
- Один HTML-файл, считанный из входных данных `html_path` и отправленный в файл `wwwroot/index.html`.

Логика модуля статической веб-страницы реализована в `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Модульный тест

Обычно Terratest применяется для тестов интеграции. Это значит, что будут подготавливать реальные ресурсы в реальной среде. Иногда такие задания могут стать чрезвычайно большими, особенно в том случае, если необходимо подготовить к работе множество ресурсов. Хорошим примером является описанная в предыдущем разделе логика преобразования имен учетной записи хранения. Нам не нужно подготавливать все ресурсы, необходимо просто убедиться в правильности логики преобразования имен.

Благодаря гибкости Terratest это легко реализуется с помощью модульных тестов. Модульные тесты — это локальные тестовые случаи (доступ в Интернет все равно нужен), которые просто выполняют команды `terraform init` и `terraform plan`. Модульные тестовые случаи будут анализировать выходные данные `terraform plan` и искать значения атрибутов для сравнения.

В оставшейся части этого раздела вы узнаете, как мы используем Terratest для реализации модульного теста, чтобы убедиться в правильности логики преобразования имен учетных записей хранения. Нас интересуют только файлы, отмеченные звездочкой `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Сначала очистите HTML-файл, `./test/fixtures/storage-account-name/empty.html` — это просто заполнитель.

В файле `./test/fixtures/storage-account-name/main.tf` приведена структура тестового случая. Он принимает одни входные данные `website_name`, которые также являются входными данными модульных тестов. Здесь отображается его логика:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Наконец, основной компонент — это реализация модульных тестов: `./test/storage_account_name_unit_test.go`

Если вы являетесь разработчиком Go, вы узнаете, что принимая аргумент типа `*testing.T`, он соответствует подписи классической функции проверки Go.

В тексте модульного теста есть всего пять случаев, определенных в переменной `testCases` (ключ является входными данными, а значение — выходными). Для каждого случая модульного теста сначала выполняется команда `terraform init`, целью которой является папка средства тестирования (`./test/fixtures/storage-account-name/`). 

После этого команда `terraform plan` с определенными входными данными тестового случая (ознакомьтесь с определением `website_name` в `tfOptions`) сохранит результат в `./test/fixtures/storage-account-name/terraform.tfplan` (который не указан в общей структуре папок).

Затем этот файл результатов будет проанализирован и преобразован в структуру для считывания в коде с помощью официального средства синтаксического анализа плана Terraform.

Теперь мы будем искать атрибуты, которые нас интересуют (в данном случае `name` из `azurerm_storage_account`) и сравнивать их с ожидаемым результатом.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Для выполнения модульных тестов требуется выполнить следующие действия в командной строке.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Традиционный результат теста Go отобразится примерно через минуту.

### <a name="integration-test"></a>Тест интеграции

В отличие от модульных тестов тесты интеграции необходимы для подготовки ресурсов к реальной среде с точки зрения комплексной перспективы. Terratest хорошо справляется с такими задачами. Так как в рекомендации для модуля Terraform также говорится об использовании папки `examples`, содержащей комплексные примеры, почему бы просто не проверить эти примеры в виде тестов интеграции? В этом разделе мы сосредоточимся на трех файлах, каждый из которых отмечен звездочкой `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Давайте начнем с примеров. В папке `./examples/` создается пример папки под названием `hello-world/`. В этом разделе представлена простая страница HTML для отправки `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Пример Terraform `./examples/hello-world/main.tf` похож на тот, который был показан в модульном тесте, только с одной большой разницей: он также выводит URL-адрес отправленного HTML, который называется `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest и классическая функция проверки Go еще раз появляются в файле теста интеграции `./test/hello_world_example_test.go`.

В отличие от модульных тестов тесты интеграции создают фактические ресурсы в Azure, поэтому необходимо быть осторожным и избегать конфликтов имен. (Обратите особое внимание на некоторые глобально уникальные имена, такие как имя учетной записи хранения). Таким образом, первым шагом логики тестирования является создание случайного `websiteName` с помощью функции `UniqueId()`, предоставляемой TerraTest. Эта функция создает случайное имя, содержащее буквы в нижнем регистре, верхнем регистре или числа. `tfOptions` указывает, что все команды Terraform должны выполняться с папкой `./examples/hello-world/`, а также гарантирует, что для `website_name` задано случайное значение `websiteName`.

Затем одна за другой выполняются команды `terraform init`, `terraform apply`, и `terraform output`. Мы использовали другую вспомогательную функцию `HttpGetWithCustomValidation()`, предоставляемую Terratest, чтобы убедиться, что HTML отправлен по URL-адресу `homepage` выходных данных, возвращаемому в `terraform output`, путем сравнения кода состояния HTTP Get с кодом `200` и поиска определенных ключевых слов в содержимом HTML. Наконец, обеспечивается выполнение `terraform destroy` с помощью функции `defer` Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Для выполнения тестов интеграции требуется выполнить следующие действия в командной строке.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Традиционный результат теста Go отобразиться примерно через две минуты. Конечно, вы также можете выполнить оба модульных теста и теста интеграции, выполнив следующий код:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Как вы видите, тесты интеграции занимают гораздо больше времени, чем модульные (две минуты для одного случая интеграции и одна минута для пяти модульных случаев). Однако только вы выбираете, когда использовать тесты (модульные тесты и тесты интеграции). Обычно мы предпочитаем применять модульные тесты для сложной логики с использованием функций Terraform HCL, а тесты интеграции — с точки зрения комплексной перспективы пользователя.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Упрощение обработки случаев Terratest с помощью mage 

Как вы уже видели, выполнение тестовых случаев в оболочке — непростая задача, так как вам необходимо переходить к различным каталогам и выполнять различные команды. Именно поэтому мы представляем систему сборки в нашем проекте. В этом разделе для выполнения задания мы будем использовать mage системы сборки Go.

Единственное, что требуется mage, это файл `magefile.go` в корневом каталоге вашего проекта (помечен символом `(+)` на следующем рисунке).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Вот пример `./magefile.go`. В этом скрипте сборки на языке Go мы реализовали пять шагов сборки:
- `Clean`. Этот шаг приведет к удалению всех созданных и временных файлов во время выполнения тестов.
- `Format`. На этом шаге будет выполняться `terraform fmt` и `go fmt` для форматирования базы кода.
- `Unit`. На этом шаге будут выполнены все модульные тесты (включая соглашение об именовании функции `TestUT_*`) в папке `./test/`.
- `Integration`. Аналогично `Unit`, но в отличие от модульных тестов, на этом этапе выполняются интеграционные тесты (`TestIT_*`).
- `Full`. На этом шаге последовательно выполняются `Clean`, `Format` и `Unit', and `Integration`.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Как и на предыдущих шагах, для выполнения полного набора тестов можно использовать следующие команды:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Вы можете заменить последнюю командную строку любыми шагами mage, например `mage unit` или `mage clean`. Вы можете подумать, что здесь все еще много командных строк, и внедрять команды `dep` и `az login` в magefile — хорошая идея. Мы не будем показывать здесь код. Очередным этапом использования mage является возможность совместного использования шагов с помощью системы упаковки Go. Таким образом, magefiles во всех модулях можно упростить, просто ссылаясь на общую реализацию и объявление зависимостей (`mg.Deps()`).

> [!NOTE]
> **Вариант. Установка переменных среды субъект-службы для выполнения тестов приемки**
> 
> Вместо выполнения команды `az login` перед тестом проверку подлинности Azure можно выполнить, задав переменные среды субъекта-службы. Terraform выводит [список имен переменных среды](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Требуются только первые четыре переменных среды.) Terraform также выводит информационные инструкции о том, как [получить значение этих переменных среды.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Terratest см. [на странице GitHub](https://github.com/gruntwork-io/terratest). Некоторые полезные сведения о команде mage вы можете найти на [странице GitHub о ней](https://github.com/magefile/mage) и [на ее домашней странице](https://magefile.org/).
