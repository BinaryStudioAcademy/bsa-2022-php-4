# Binary Studio Academy 2022

## Домашнее задание #4 ([ua](README_UA.md))

### Требования

Это задание направлено на развитие навыков проектирования архитектуры приложения с использованием принципов ООП и паттернов проектирования. В частности затрагивается тема обработки команд и ошибок, управление состоянием и жизненным циклом объектов, геймификация.

***

### Установка

Установка представлена в рабочем окружении OS Linux:

```bash
git clone https://github.com/BinaryStudioAcademy/bsa-2022-php-4.git
cd bsa-2022-php-4
composer install
```

### Задание
Вашим заданием является разработка консольной игры "Galaxy Warriors".

Игрок - военно-разведывательный космический корабль, которому необходимо найти и уничтожить главный вражеский космический корабль инопланетян в одной из галактик.

Космический корабль игрока обладает следующими свойствами:

```php
int strength // определяет урон, наносимый лазерной установкой (1 до 10)
int armor   // уровень защиты, отражаемый при атаке вражеским космическим кораблем (1 - 10)
int luck     // определяет удачу попадания в слабо защищенные элементы вражеского корабля (1-10)
int health   // определяет текущий уровень силовой защиты корабля в целом (1 - 100)
array hold   // грузовой отсек корабля для хранения и транспортировки захваченных ресурсов, вместительность: 3 элемента.
```

С космического корабля, который удалось разбить, можно получить редкий космический кристалл (🔮) или силовую магнитную установку (🔋). Кристалл можно обменять на улучшение на единицу одной из характеристик: strength, armor. Или на одну магнитную установку, применение которой восстанавливает уровень силовой защиты (health) на 20 единиц.

Игровой мир состоит из 7 галактик:

- 1 домашня галактика, в которой нет космических кораблей враждебных инопланетян, которая является отправной точкой в игре (`Home Galaxy`). Здесь же можно обменять собранные кристаллы.

- 3 галактики (`Andromeda`, `Pegasus`, `Spiral`) с патрульными космическими кораблями (`Patrol Spaceship`) со скромными характеристиками:
```
    strength: 3-4
    armor: 2-4
    luck: 1-2
    health: 100
    hold: [ 🔋 _ _ ]
```

- 2 галактики (`Shiar`,`Xeno`) охраняемые боевыми космическими кораблями (`Battle Spaceship`) со следующими характеристиками:
```
    strength: 5-8
    armor: 6-8
    luck: 3-6
    health: 100
    hold: [ 🔋 🔮 _ ]
```

- И 1 галактика (`Isop`) с мега-крейсером (`Executor`), обладающим всеми свойствами по-максимуму:
```
    strength: 10
    armor: 10
    luck: 10
    health: 100
    hold: [ 🔋 🔮 🔮 ]
```

Старт игры происходит в Home Galaxy. Космический корабль изначально имеет следующие характеристики:

```
    strength: 5
    armor: 5
    luck: 5
    health: 100
```

Посещать галактики можно неограниченное количество раз. При этом с каждым посещением в галактике должен генерироваться новый корабль соответствующий галактике.

Если игрок проигрывает, то игра начинается сначала. Корабль получает изначальные характеристики.

Главная цель игры: уничтожить мега-крейсер, после чего игра считается завершенной.

#### Команды игры:

- `help` - выводит список доступных команд

- `stats` - выводит характеристики корабля и содержимое грузового отсека

- `set-galaxy <home|andromeda|spiral|pegasus|shiar|xeno|isop>` - совершить прыжок в указанную галактику

- `attack` - произвести выстрел в космический корабль противника. Сражение происходит пошагово, первый выстрел осуществляет игрок, затем NPC. Вероятность попадания должна зависеть от уровня удачи корабля. А нанесенный урон от силы и защиты (необходимо использование хелпера Math).

- `grab` - собрать полезный груз с побежденного корабля.

- `buy <strength|armour|reactor>` - улучшить 1 из характеристик или купить магнитную установку. Покупать можно только в Home Galaxy в обмен на космический кристалл по курсу 1:1.

- `apply-reactor` - применить магнитную установку, увеличив на 20 единиц общее магнитное поле корабля (health).

- `whereami` - выводит название галактики, в которой находится космический корабль

- `restart` - перезапускает игру

- `exit` - выход из игры

#### Режимы игры

```php
/**
 * Запускает игру в бесконечном цикле и опрашивает пользовательский ввод.
 */
public function start(Reader $reader, Writer $writer);

/**
 * Пошаговое выполнение.
 * Запускает игру и выполняет только одну команду с $reader'a. При этом состояние игрового мира должно сохранятся
 * Этот режим необходим для тестирования.
 */
public function run(Reader $reader, Writer $writer);
```

Для того, чтобы тесты работали корректно, необходимо использовать предоставленные хелперы Math и Random:

Random::get(): float - возвращает случайное число от 0 до 1. Обратите внимание, что инстанс Random передается в конструктор игры, больше нигде он инстанцироваться не должен, иначе тесты работать не будут.

Math::luck(Random, int): bool - рассчитывает значение попадания в корабль (true or false), на основании характеристики удачи корабля который осуществляет выстрел. Обратите внимание, что реализация использует константу Stats::MAX_LUCK, которая определяет максимальное значение характеристики удачи.

Math::damage(int, int): int - рассчитывает наносимый урон, на основании силы лазера атакующего корабля, и брони защищающегося корабля.

Также обратите внимание на класс `tests\Game\Messages`, здесь представлены сообщения, которые ожидаются в тесте.

### Проверка

- Игра должна запускаться через терминал и исполнять команды через пользовательский ввод в соответствии с условиями задания.

```bash
php game.php
```

- Проверить себя можно с помощью юнит тестов:

```bash
./vendor/bin/phpunit
```

### Прием решений

Необходимо склонировать этот репозиторий и разместить свое решение на Bitbucket.

__Форкать репозиторий категорически запрещено!__

Выполненное задание будет оценивается по следующим критериям:

1) Игра работает в соответствии с заданием: 5 баллов (будьте внимательны и не ориентируйтесь исключительно на удачно пройденные тесты. Они могут покрывать не все случаи)

2) Обработаны исключительные ситуации (значения здоровья и характеристик корабля не превышают максимальное количество, нельзя перейти в несуществующую галактику и т.д.): 1 балл

3) Использовано минимум [3-и паттерна проектирования](https://designpatternsphp.readthedocs.io/en/latest/) (hint!: для выполнения задачи подойдут паттерны Command, Builder, Factory): 3 балла

4) Код написан чисто и аккуратно в соответствии со стандартом [PSR-2](https://www.php-fig.org/psr/psr-2/) и [PSR-12](https://www.php-fig.org/psr/psr-12/), без закоментированых отладочных блоков и функций в коде: 1 балл


### Запуск

Разрешается использовать любое тестовое окружение с PHP =>8.1.

Или использовать предоставленное docker окружение. Для этого достаточно сбилдить image:

```
docker-compose build
```

Установить зависимости:

```
docker-compose run --rm composer install
```

Запустить приложение:

```
docker-compose run --rm php php game.php
```

Запустить тесты:

```
docker-compose run --rm php ./vendor/bin/phpunit
```

Для удобства отладки также в контейнере есть XDebug, который легко подключить к вашей IDE.

Примеры для VS Code и PHP Storm можно найти в [инструкции](debug.md).
