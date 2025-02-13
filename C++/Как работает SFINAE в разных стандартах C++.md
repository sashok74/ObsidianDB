Date: 2024-12-09 - Time: 10:27
___
Tags: #QUESTION #ANSWER
___
### Question:
### Как ограничить шаблон по типу параметра или другому признаку.
___
### Answer:
SFINAE
___
### Detail:
### Сравнение:
1. **C++11**: Использует `std::enable_if<...>::type`, синтаксис громоздкий.
2. **C++14**: Добавлен `std::enable_if_t`, сокращает запись, улучшает читаемость.
3. **C++17**: Убрана необходимость в `std::enable_if`, добавлен `if constexpr` для непосредственной проверки типов.
4. **C++20**: Концепции (`requires`) делают ограничения типов частью языка, а не библиотек, значительно улучшая выразительность.

```cpp
в это выражении
template <typename T>
typename std::enable_if<!std::is_integral<T>::value, void>::type
print_ip  (T ip)
{
  std::cout << ip << std::endl;
}

//typename std::enable_if<!std::is_integral<T>::value, void>::type 
//это тип результата функции?
```


```cpp
typename std::enable_if<!std::is_integral<T>::value, void>::type
```

это **тип результата функции** (`return type`), но его использование обусловлено механикой `SFINAE` (Substitution Failure Is Not An Error). Если условие внутри `std::enable_if` не выполняется, то тип становится недействительным, и функция **не участвует в перегрузках функций** (как будто она "исключается" во время компиляции). Этот подход традиционно использовался для ограничения типов в шаблонных функциях до появления более удобных механизмов в более поздних стандартах C++.

Теперь давайте разберём, как это записывается и упрощается в стандартах C++11, C++14, C++17 и C++20.

---

### **C++11**
Ваш код полностью соответствует стандарту C++11. Здесь используется `std::enable_if`, чтобы ограничить доступ к этой функции только для типов `T`, которые **не являются целочисленными**.

Код:

```cpp
template <typename T>
typename std::enable_if<!std::is_integral<T>::value, void>::type
print_ip(T ip)
{
    std::cout << ip << std::endl;
}
```

- **Как работает?**
  1. `std::is_integral<T>::value` возвращает `true`, если `T` — это целочисленный тип (`int`, `long`, `unsigned char` и т.д.).
  2. `!std::is_integral<T>::value` инвертирует это значение.
  3. Если тип `T` не целочисленный, `std::enable_if` выставляет `typename` как `void`, и, следовательно, функция становится корректной.
  4. Если тип `T` целочисленный, то `std::enable_if` генерирует недопустимый тип, и функция исключается из компиляции (SFINAE).

---

### **C++14**
В C++14 появляется синтаксический сахар для сокращения записи. Вместо `typename std::enable_if<...>::type`, мы можем использовать `std::enable_if_t`.

Код становится компактнее:

```cpp
template <typename T>
std::enable_if_t<!std::is_integral<T>::value, void>
print_ip(T ip)
{
    std::cout << ip << std::endl;
}
```

- **Обратите внимание:** 
  - `std::enable_if_t` — это просто алиас (type alias) для `typename std::enable_if<...>::type`, поэтому логика работы остаётся прежней.
  - Использование `std::enable_if_t` улучшает читаемость кода, избавляя от дополнительного шаблонного синтаксиса `typename`.

---

### **C++17**
C++17 позволяет отказаться от `std::enable_if` благодаря новой конструкции `if constexpr`. Концепция SFINAE постепенно замещается на более простую и интуитивную проверку типов.

Код преобразуется в следующий:

```cpp
template <typename T>
void print_ip(T ip)
{
    if constexpr (!std::is_integral_v<T>) { 
    // _v - добавлено в C++17 как синтаксический сахар
        std::cout << ip << std::endl;
    }
}
```

- **Изменения:**
  1. `std::is_integral_v<T>` — это сокращение для `std::is_integral<T>::value` (введено в C++17).
  2. С помощью `if constexpr` проверка типа происходит во время компиляции. Если условие `!std::is_integral_v<T>` выполняется, то код внутри `if constexpr` компилируется. Если не выполняется, этот блок пропускается на этапе компиляции.
  3. Нет необходимости использовать `std::enable_if` для ограничения типов.

- **Преимущество:**
  - Код проще и однозначнее.
  - Мы просто описываем логику шаблона в теле функции, а не через сложные механизмы ограничения типов.

---

### **C++20**
Начиная с C++20, появляются **концепции (concepts)**, которые являются официальным языковым инструментом для ограничения шаблонов. С помощью концептов можно более естественно выражать ограничения для типов.

Код с использованием концептов:

```cpp
#include <concepts> // Для использования стандартных концептов

template <typename T>
requires (!std::integral<T>) // Концепция std::integral проверяет, является ли T целым числом
void print_ip(T ip)
{
    std::cout << ip << std::endl;
}
```

- **Объяснение:**
  1. Концепция `std::integral` встроена в библиотеку `<concepts>`. Она выполняет ту же функцию, что и `std::is_integral<T>::value`.
  2. Запись `requires (!std::integral<T>)` ограничивает шаблон так, что он подходит только для типов `T`, которые **не являются целочисленными**.

Также можно ещё больше упростить код с новым синтаксисом концептов:

```cpp
#include <concepts>

void print_ip(const auto& ip) requires (!std::integral<decltype(ip)>)
{
    std::cout << ip << std::endl;
}
```

- Здесь `auto` заменяет шаблонный параметр `<typename T>`, и явно указывается ограничение через `requires`.

---

### Подведём итоги:

#### Сравнение вариантов по стандартам:
| Стандарт | Синтаксис | Особенности |
|----------|-----------|-------------|
| **C++11** | Использует `std::enable_if` | Сложный, но работает. |
| **C++14** | Использует `std::enable_if_t` | Чище и проще за счёт алиасов. |
| **C++17** | Убирает `std::enable_if`, добавляет `if constexpr` | Компактный и интуитивный. |
| **C++20** | Использует концепты (`concepts`) | Самый читаемый и выразительный способ. |

- В C++11 и C++14 конструкция `typename std::enable_if`/`std::enable_if_t` определяет тип результата функции, что позволяет "включать" или "исключать" шаблонные функции на этапе компиляции.
- В C++17 с `if constexpr` проверка выполняется внутри функции.
- В C++20 концепты явно ограничивают тип шаблона на уровне объявления функции, делая код максимально удобным для чтения и сопровождения.
>>
как устроены под капотом std::enable_if и if constexpr
мне это надо, чтобы понимать как они работают и в чем основная разница


Давайте разберёмся, как устроены `std::enable_if` и `if constexpr` "под капотом", а также сравним их механизмы работы и основное различие.

---

## **1. Что такое `std::enable_if` и как оно работает?**

### **Основная идея**
`std::enable_if` — это метапрограммный инструмент, входящий в стандарт C++ (начиная с C++11), для **ограничения шаблонов на этапе компиляции**. Он работает через механизм **SFINAE** (Substitution Failure Is Not An Error). Если при подстановке типа условие внутри `std::enable_if` не выполняется, то шаблон будет считаться недействительным и не участвует в выборе кандидатов на перегрузку функций.

### **Определение `std::enable_if`**
В библиотеке standard template library (STL) `std::enable_if` определён примерно так (упрощённо):

```cpp
template<bool B, typename T = void>
struct enable_if {};

// Если условие `B` истинно, предоставляется член `type`:
template<typename T>
struct enable_if<true, T> {
    using type = T; // Объявим результат - тип T
};
```

- Это так называемая **частичная специализация шаблона**.
- Когда первое значение шаблона `B` равно `true`, генерируется структура со вложенным типом `type`, который равен второму аргументу шаблона `T`.
- Если `B == false`, член `type` отсутствует, и шаблон становится недействительным (ошибка **SFINAE**, но компилятор не прерывает процесс).

#### **Пример в действии**
```cpp
template <typename T>
typename std::enable_if<!std::is_integral<T>::value, void>::type
print_ip(T ip) {
    std::cout << ip << std::endl;
}
```

**Шаги компиляции:**
1. Компилятор подставляет тип `T`.
2. Проверяется выражение внутри `std::enable_if`: 
   - Например, если `T = int`, то `!std::is_integral<int>::value` эквивалентно `false`, и, следовательно, вложенный член `type` не существует.
   - Если `T = std::string`, то `!std::is_integral<std::string>::value` равно `true`, и `type` определён как `void`.

**Ключевой момент:** Если `B == false`, функция просто **исключается из кандидатов на этапе перегрузки функций**.

---

### **Что такое SFINAE?**
SFINAE (Substitution Failure Is Not An Error) — это правило, по которому ошибка подстановки шаблона (например, `std::enable_if`) **не считается ошибкой компиляции**, а просто исключает данный шаблон из возможных кандидатов в процессе компиляции. Это позволяет обеспечить выбор подходящего шаблона для переданного типа (или отклонить неподходящий).

---

## **2. Как работает `if constexpr` в C++17?**

### **Основная идея**
`if constexpr` был введён в C++17 как способ выполнить **условную компиляцию на этапе компиляции**. Его основное отличие от `std::enable_if` заключается в том, что с помощью `if constexpr` можно включать или исключать код *внутри функции*, а не на этапе выбора шаблона.

### **Как он устроен**
`if constexpr` — это **ключевое слово языка**, встроенное в компилятор. Если условие внутри `if constexpr` приводится к `false` на этапе компиляции, блок внутри `if constexpr` **не компилируется вообще**.

Это происходит по следующему механизму:
1. Компилятор анализирует выражение в `if constexpr` ещё до генерации машинного кода.
2. Если условие является `true`, код внутри `if constexpr` включается как часть исполняемой программы.
3. Если условие является `false`, соответствующий блок кода исключается; это эквивалентно отсутствию данного кода.

**Пример:**

```cpp
template <typename T>
void print_ip(T ip) {
    if constexpr (std::is_integral_v<T>) {
        std::cout << "Integral type: " << ip << std::endl;
    } else {
        std::cout << "Non-integral type: " << ip << std::endl;
    }
}
```

- Если вызывается `print_ip(42)`, компилятор компилирует только ветку `if constexpr (std::is_integral_v<T>)`.
- Если вызывается `print_ip("Hello")`, ветка `else` компилируется, а ветка `if constexpr` исключается.

### **В чём ключевая разница между `if constexpr` и `std::enable_if`?**
`std::enable_if` работает путём **ограничения типов при выборе перегрузки функции**, в то время как `if constexpr` позволяет **отключать/включать код внутри одной функции**.

---

## **3. Основное различие между `std::enable_if` и `if constexpr`**

| Характеристика                 | `std::enable_if`                           | `if constexpr`                       |
|--------------------------------|--------------------------------------------|--------------------------------------|
| **Этап работы**                | Ограничения на этапе выбора шаблонной функции. | Условная компиляция **внутри функции**. |
| **Уровень применения**         | На уровне перегрузок функций или шаблонов. | На уровне логики внутри функции.     |
| **SFINAE**                     | Основан на SFINAE (шаблон исключается при ошибке подстановки). | SFINAE не используется.             |
| **Простота использования**     | Сложнее, так как нужно добавлять ограничения к типу шаблона. | Проще, так как проверка внутри тела кода. |
| **Гибкость**                   | Подходит для сложных ограничений шаблонов. | Удобен для упрощения внутренней логики. |
| **Синтаксис**                  | Громоздкий для сложных случаев.            | Более компактный и понятный.         |

---

### **Ключевые сценарии использования**

- Используйте **`std::enable_if`**, если вам нужно **ограничить типы**, для которых будет доступна функция (например, разрешить шаблон только для определённых типов, таких как `std::string` или `std::vector`).
  
- Используйте **`if constexpr`**, если хотите написать одну функцию, но поведение (выполняемый код) должно зависеть от типа переданного шаблонного аргумента. 

**Пример сравнения**:

```cpp
// С помощью std::enable_if
template <typename T>
typename std::enable_if<!std::is_integral<T>::value, void>::type
print_ip(T ip) {
    std::cout << ip << std::endl;
}

// С помощью if constexpr
template <typename T>
void print_ip(T ip) {
    if constexpr (!std::is_integral_v<T>) {
        std::cout << ip << std::endl;
    }
}
```

Оба варианта работают одинаково, однако `if constexpr` значительно проще и интуитивнее читать.

---

**Выводы:**
1. `std::enable_if` — инструмент для ограничения функций или шаблонов на этапе компиляции. Он работает через SFINAE.
2. `if constexpr` — гибкое средство для условного исключения кода внутри функции, введённое в стандарт языка C++17.
3. Основное различие в том, что `std::enable_if` исключает шаблоны из перегрузки, а `if constexpr` управляет компиляцией кода внутри функции.

___
### Zero-Links

___
### Links
