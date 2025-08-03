
```cpp
#include <iostream>
#include <memory>
#include <string>

// Абстрактные интерфейсы для UI-элементов
class Button {
public:
    virtual void render() const = 0;
    virtual ~Button() = default;
};

class TextField {
public:
    virtual void render() const = 0;
    virtual ~TextField() = default;
};

// Пространства имён для стилей
namespace Style {
    // Стиль "Modern"
    namespace Modern {
        class Button : public ::Button {
            std::string label;
        public:
            Button(const std::string& lbl) : label(lbl) {}
            void render() const override {
                std::cout << "Modern Button: [" << label << "]\n";
            }
        };

        class TextField : public ::TextField {
            std::string placeholder;
        public:
            TextField(const std::string& ph) : placeholder(ph) {}
            void render() const override {
                std::cout << "Modern TextField: {" << placeholder << "}\n";
            }
        };
    }

    // Стиль "Classic"
    namespace Classic {
        class Button : public ::Button {
            std::string label;
        public:
            Button(const std::string& lbl) : label(lbl) {}
            void render() const override {
                std::cout << "Classic Button: <" << label << ">\n";
            }
        };

        class TextField : public ::TextField {
            std::string placeholder;
        public:
            TextField(const std::string& ph) : placeholder(ph) {}
            void render() const override {
                std::cout << "Classic TextField: [" << placeholder << "]\n";
            }
        };
    }
}

// Шаблонный Factory Method для создания объектов
template <typename T, typename... Args>
std::unique_ptr<T> createElement(Args&&... args) {
    return std::make_unique<T>(std::forward<Args>(args)...);
}

// Шаблонная Abstract Factory с использованием стиля как параметра
template <typename Style>
class DialogFactory {
public:
    std::unique_ptr<Button> createButton(const std::string& label) const {
        return createElement<typename Style::Button>(label);
    }

    std::unique_ptr<TextField> createTextField(const std::string& placeholder) const {
        return createElement<typename Style::TextField>(placeholder);
    }

    void createDialogForm(const std::string& buttonLabel, const std::string& textFieldPlaceholder) const {
        auto button = createButton(buttonLabel);
        auto textField = createTextField(textFieldPlaceholder);

        std::cout << "Rendering Dialog Form:\n";
        button->render();
        textField->render();
    }
};

// Клиентский код
int main() {
    // Создаём фабрику для Modern-стиля
    DialogFactory<Style::Modern> modernFactory;
    std::cout << "Modern Dialog Form:\n";
    modernFactory.createDialogForm("Submit", "Enter your name");

    std::cout << "\n";

    // Создаём фабрику для Classic-стиля
    DialogFactory<Style::Classic> classicFactory;
    std::cout << "Classic Dialog Form:\n";
    classicFactory.createDialogForm("OK", "Type here");

    return 0;
}
```