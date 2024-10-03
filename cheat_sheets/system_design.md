# System design

## Курсы
* [ML for Developers: Design, Develop, Deploy, Iterate](https://madewithml.com/#mlops)

## Минимальная конфигурация проекта

* Документация
    * README.md / INSTALL.md с инструкцией по запуску
    * Аннотации у функций и классов
* Гибкие настройки
    * Yaml-конфиги, env-переменные окружения
* Форматирование кода
    * PEP-8, кодстайлы
    * Линтеры (например, mypy) и форматеры кода (например, black)
* Работа в команде
    * Воспроизводимость экспериментов (requirements.txt и виртуальные окружения)
    * Треккинг задач
    * git-flow

## Разработка архитектуры приложения
* [Refactoring Guru: Design Patterns](https://refactoring.guru/design-patterns)
* [Complete Guide to Caching in Python](https://towardsdatascience.com/complete-guide-to-caching-in-python-b4e37a4bcebf)

## Проектирование кода

### Базовые принципы декомпозиции
* Название функции - глагол
* Одна функция == одно действие
* [Пара слов об именовании переменных и методов](https://habr.com/ru/articles/508238/)

### SOLID
* **Single Responsibility** (принцип единственной ответственности)
  * Один класс == одна задача
* **Open-closed** (принцип открытости/закрытости)
  * Программные сущности должны быть расширяемыми без изменения своего содержимого
* **Liskov substitution** (принцип подстановки)
  * Наследующий класс должен дополнять, а не изменять базовый
* **Interface segregation** (принцип разделения интерфейсов)
  * Интерфейс нужно подразбивать на много малых интерфейсов, чтобы не заставлять клиента пользоваться ненужным
* **Dependency inversion** (принцип инверсии зависимостей)
  * Высокоуровневые модули не должны зависеть от низкоуровневых
  * Абстракции не должны зависеть от подробностей
  * Подробности должны зависеть от абстракций

### Принципы написания кода
* KISS == keep it simple, stupid (бритва Окама)
* EAFP == it's easy to ask for forgiveness than permission
* LBYL == look before you leap
* DRY == don't repeat yourself
* YAGNI == you're aren't gonna need it (отказ от избыточности)
* Worse is better:
  * простота
  * правильность
  * логичность
  * полнота
