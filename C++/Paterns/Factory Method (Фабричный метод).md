```cpp
#include <iostream>
#include <string>

// Абстрактный продукт
class Vehicle {
public:
    virtual void drive() const = 0; // Чисто виртуальный метод
    virtual ~Vehicle() = default;   // Виртуальный деструктор
};

// Конкретный продукт 1: Автомобиль
class Car : public Vehicle {
public:
    void drive() const override {
        std::cout << "Driving a car\n";
    }
};

// Конкретный продукт 2: Мотоцикл
class Motorcycle : public Vehicle {
public:
    void drive() const override {
        std::cout << "Riding a motorcycle\n";
    }
};

// Абстрактный создатель (фабрика)
class VehicleFactory {
public:
    virtual Vehicle* createVehicle() const = 0; // Фабричный метод
    virtual ~VehicleFactory() = default;

    // Дополнительный метод для удобства использования
    void useVehicle() const {
        Vehicle* vehicle = createVehicle();
        vehicle->drive();
        delete vehicle; // Очистка памяти
    }
};

// Конкретный создатель 1: Фабрика автомобилей
class CarFactory : public AppendingCarFactory : public VehicleFactory {
public:
    Vehicle* createVehicle() const override {
        return new Car();
    }
};

// Конкретный создатель 2: Фабрика мотоциклов
class MotorcycleFactory : public VehicleFactory {
public:
    Vehicle* createVehicle() const override {
        return new Motorcycle();
    }
};

// Клиентский код
int main() {
    // Создаем фабрики
    VehicleFactory* carFactory = new CarFactory();
    VehicleFactory* motorcycleFactory = new MotorcycleFactory();

    // Используем фабрики для создания и использования объектов
    std::cout << "Using Car Factory:\n";
    carFactory->useVehicle();

    std::cout << "Using Motorcycle Factory:\n";
    motorcycleFactory->useVehicle();

    // Очистка памяти
    delete carFactory;
    delete motorcycleFactory;

    return 0;
}
```

### Как это работает?

1. **Абстрактный продукт (Vehicle)**:
    - Определяет интерфейс для всех объектов, которые будут создаваться (в данном случае — метод drive()).
2. **Конкретные продукты (Car, Motorcycle)**:
    - Реализуют интерфейс Vehicle, предоставляя конкретное поведение (например, вывод сообщения о вождении).
3. **Абстрактный создатель (VehicleFactory)**:
    - Определяет фабричный метод createVehicle(), который должен быть реализован в подклассах.
    - Дополнительно есть метод useVehicle(), который демонстрирует использование созданного объекта.
4. **Конкретные создатели (CarFactory, MotorcycleFactory)**:
    - Переопределяют createVehicle(), возвращая экземпляры конкретных продуктов (Car или Motorcycle).
5. **Клиентский код (main)**:
    - Создает фабрики и использует их для создания объектов, не зная их конкретных типов.

### Почему это Factory Method?

- Клиент работает с абстрактным типом VehicleFactory и не знает, какой конкретно объект создаётся (Car или Motorcycle).
- Логика создания делегирована подклассам (CarFactory, MotorcycleFactory), что соответствует сути паттерна.
- Расширяемость: чтобы добавить новый тип транспорта (например, Truck), достаточно создать новый класс и фабрику, не меняя существующий код.

Этот пример демонстрирует гибкость и изоляцию создания объектов, что является ключевой задачей Factory Method.