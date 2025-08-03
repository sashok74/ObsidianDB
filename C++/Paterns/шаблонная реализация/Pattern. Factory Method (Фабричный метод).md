```cpp
#include <iostream>
#include <memory>
#include <string>
#include <vector>

// Абстрактный базовый класс для UI-элементов
class UIElement {
public:
    virtual void render() const = 0;
    virtual ~UIElement() = default;
};

// Конкретный UI-элемент: Кнопка (1 параметр - текст)
class Button : public UIElement {
    std::string label;
public:
    Button(const std::string& lbl) : label(lbl) {}
    void render() const override {
        std::cout << "Rendering Button with label: " << label << "\n";
    }
};

// Конкретный UI-элемент: Текстовое поле (1 параметр - плейсхолдер)
class TextField : public UIElement {
    std::string placeholder;
public:
    TextField(const std::string& ph) : placeholder(ph) {}
    void render() const override {
        std::cout << "Rendering TextField with placeholder: " << placeholder << "\n";
    }
};

// Конкретный UI-элемент: Листбокс (2 параметра - список и индекс)
class ListBox : public UIElement {
    std::vector<std::string> items;
    int selectedIndex;
public:
    ListBox(const std::vector<std::string>& itms, int idx) 
        : items(itms), selectedIndex(idx) {
        if (idx < 0 || idx >= static_cast<int>(items.size())) {
            selectedIndex = 0; // Защита от некорректного индекса
        }
    }
    void render() const override {
        std::cout << "Rendering ListBox with items: ";
        for (const auto& item : items) {
            std::cout << item << " ";
        }
        std::cout << "\nSelected item: " << items[selectedIndex] << " (index " << selectedIndex << ")\n";
    }
};

// Шаблонная фабрика с вариативными параметрами
template <typename T>
class UIFactory {
public:
    // Вариативный метод create, принимающий произвольное число аргументов
    template <typename... Args>
    static std::unique_ptr<UIElement> create(Args&&... args) {
        return std::make_unique<T>(std::forward<Args>(args)...);
    }
};

// Клиентский код
int main() {
    // Создание кнопки (1 аргумент)
    auto button = UIFactory<Button>::create("Click Me");

    // Создание текстового поля (1 аргумент)
    auto textField = UIFactory<TextField>::create("Enter text here");

    // Создание листбокса (2 аргумента)
    std::vector<std::string> items = {"Item1", "Item2", "Item3"};
    auto listBox = UIFactory<ListBox>::create(items, 1);

    // Отрисовка элементов
    button->render();
    textField->render();
    listBox->render();

    return 0;
}
```

### Как это работает?

1. **Абстрактный класс UIElement**:
    - Остаётся неизменным, задаёт интерфейс для всех UI-элементов.
2. **Конкретные классы**:
    - Button и TextField принимают по одному параметру (std::string).
    - ListBox принимает два параметра: std::vector<std::string> (список элементов) и int (индекс выбранного элемента). Добавлена базовая проверка индекса.
3. **Шаблонная фабрика UIFactory<T>**:
    - Использует вариативный шаблон template <typename... Args> в методе create().
    - std::forward<Args>(args)... передаёт аргументы конструктору класса T без лишнего копирования, поддерживая произвольное число параметров.
4. **Клиентский код**:
    - Создаёт элементы с разным числом параметров:
        - Button — 1 аргумент.
        - TextField — 1 аргумент.
        - ListBox — 2 аргумента (вектор и индекс).