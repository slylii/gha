# YAML CV
Этот репозиторий содержит резюме в формате [yaml-cv](https://github.com/haath/yaml-cv).

## Настройка

### Локальное окружение
Установите [yq v4](https://mikefarah.gitbook.io/yq/), например:
```shell
brew install yq@4
```

Установите [yaml-cv](https://github.com/haath/yaml-cv):
```shell
gem install yaml-cv
```

Для создания PDF-версии, требуется `wkhtmltopdf`:
```shell
brew install wkhtmltopdf
```

Установите [Taskfile](https://taskfile.dev):
```shell
brew install go-task/tap/go-task
```

### Docker


### Hooks
Для автоматической проверки корректности синтаксиса YAML, проект использует [pre-commit](https://pre-commit.com).

Требуется [установить](https://pre-commit.com/#installation) его любым удобным способом.

Например, для MacOS X:
```shell
brew install pre-commit
```

А затем инициализировать:
```shell
pre-commit install
pre-commit run --all-files
```

## Использование

```shell
task --list-all
```

### Обновление информации

Исходный код включает 2 компонента:
- [src/yamlcv.yaml](src/yamlcv.yaml)

  Шаблон резюме в формате [yaml-cv](https://github.com/haath/yaml-cv)
  Сюда добавляется актуальная информация не касающаяся технических навыков.
- [src/skills.yaml](src/skills.yaml)

  Список навыков в расширенном формате. Навык должен обязательно включать `category` и `name`, а так же любой набор дополнительных полей.

### Сборка версии резюме

Запустите:
```shell
task build
```

### Конвертация в JSON

```shell
task json
```

### Поиск

Вывод навыков по уровню владения (`junior|middle|senior`):
```shell
task skill_by_level LEVEL=
```

### Docker
Все операции рекомендуется запускать внутри docker-контейнера.

Имеется возможность использования двух образов:
- **builder** (`cv-builder`)

  Включает исходный код и средства автоматизации (Taskfile).

- **release** (`cv`)

  Содержит только собранную HTML-версию резюме.

#### Сборка

Все образы сразу:
```shell
task docker-build
```

Или по-отдельности:
```shell
task docker-builder
task docker-release
```

Существует возможность использования различных базовых образов:
- Alpine ([Dockerfile.alpine](Dockerfile.alpine)) (*используется по-умолчанию*)
- Ubuntu ([Dockerfile.ubuntu](Dockerfile.ubuntu))

Для переопределения базового образа, используйте `BASE_IMAGE`
```shell
task docker-builder BASE_IMAGE=ubuntu
```

Для переопределения тэга, используйте `IMAGE_TAG`:
```shell
task docker-builder BASE_IMAGE=ubuntu IMAGE_TAG=mytag
```

#### Запуск Taskfile внутри контейнера

```shell
task docker-run-task -- *аргументы Taskfile*
```

`docker-run-task` запускает сборку `builder` образа, и поддерживает тот же набор параметров что и задачи сборки.

Пример:
```shell
task docker-run-task BASE_IMAGE=ubuntu IMAGE_TAG=test -- skill_by_level LEVEL=middle
```

#### Запуск веб-сервера 
Веб-сервер запускается средствами docker-compose и отдаёт HTML версию резюме

Запуск:
```shell
task docker-server-foreground
```

Фоновый запуск:
```shell
task docker-server-background
```

Остановка и очистка:
```shell
task docker-server-stop
```