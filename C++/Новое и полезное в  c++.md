
### 1.  Работа со строками.
[Шесть удобных операций для обработки строк в C ++ 20/23](https://www.cppstories.com/2023/six-handy-ops-for-string-processing/)
модный способ использования - std::stringstream но он медленный!
способы ускорения использование представления строки. и std::span
который разобьет строку по символам.
```cpp
#include <iostream>
#include <sstream>

void* operator new(std::size_t sz){
    std::cout << "Allocating: " << sz << '\n';
    return std::malloc(sz);
}

int main() {
    std::cout << "start...\n";
    std::stringstream str;
    str << 42;
    str << " Hello C++20/23 Programming World";
    std::cout << "print with str()\n";
    std::cout << str.str() << '\n';
    std::cout << "another try...\n";
    std::cout << str.view() << '\n';
}
```
Если вам нужно использовать `stringstream`, и предыдущие методы не принесли вам никакой пользы, и вы хотите получить полный контроль над внутренней памятью, в C ++ 23 вы можете легко обновить свой код с помощью `spanstream`!

Короче говоря, это потоковый класс, который работает с промежутками (также из C ++ 20). Взгляните на этот упрощенный пример.:
```cpp
#include <iostream>
#include <sstream>
#include <spanstream> // << new headeer!

void* operator new(std::size_t sz){
    std::cout << "Allocating: " << sz << '\n';
    return std::malloc(sz);
}

int main() {
    std::cout << "start...\n";
    std::stringstream ss;
    ss << "one string that doesn't fit into SSO";
    ss << "another string that hopefully won't fit";

    std::cout << "spanstream:\n";
    char buffer[128] { 0 };
    std::span<char> spanBuffer(buffer);
    std::basic_spanstream<char> ss2(spanBuffer);
    ss2 << "one string that doesn't fit into SSO";
    ss2 << "another string that hopefully won't fit";

    std::cout << buffer;
}
```
### 2. Хитрый способ использовать перегрузку New
```cpp
#include <iostream>
#include <sstream>

void* operator new(std::size_t sz){
    std::cout << "Allocating: " << sz << '\n';
    return std::malloc(sz);
}
```
мы будет видеть сколько памяти выделилось . на операции.

### 3. Используем std::expect для возврата результата или ошибки.

в C ++ 23. `std::expected` это тип, специально разработанный для возврата результатов из функции вместе с дополнительной информацией об ошибке.

```cpp
std::expected<DatabaseRecord, std::string> findRecord(Database& db, int recordId) noexcept {
    if (!db.connected())
        return std::unexpected("DB not connected");

    auto record = db.query(recordId);
    if (record.valid)
        return record;

    return std::unexpected("Record not found");
}

int main() {
    Database myDatabase;
    auto result = findRecord(myDatabase, 1024);
    if (result)
        std::cout << "got record " << result.value().valid << '\n';
    else
        std::cout << result.error();
}
```