# 🛠️ Рефакторинг доступа к данным MERP: универсальные источники

## 1. Цель
- Отвязать бизнес-структуры и логику от конкретного способа получения данных (БД, JSON, тестовые моки).
- Упростить тестирование, расширяемость и поддержку кода.

---

## 2. Архитектурные принципы
- **Структуры данных** (например, `OutParam`, `MyData`) не должны зависеть от способа заполнения.
- **Логика заполнения** (fill) реализуется через универсальные адаптеры (Loader-ы).
- **Контейнеры** заполняются через абстрактный интерфейс `ILoader`.
- Для каждого источника данных реализуется свой Loader (например, для БД — `RecordsetLoader`, для JSON — `JsonLoader`).

---

## 3. Пошаговый план рефакторинга

### 3.1. Вынесите логику заполнения из структур
- Удалите методы типа `FromRecordset` из структур.
- Реализуйте универсальный шаблон:
  ```cpp
  template<typename Source>
  static void FromSource(Source& src, MyData& m) {
      src.fill("id", m.id);
      src.fill("name", m.name);
      // ...
  }
  ```

### 3.2. Внедрите абстракцию Loader
- Создайте интерфейс:
  ```cpp
  class ILoader {
  public:
      virtual ~ILoader() = default;
      virtual bool Next() = 0;
      virtual void FillCurrent(void* item) = 0;
  };
  ```

### 3.3. Реализуйте Loader-ы для разных источников
- Для БД: `RecordsetLoader<T>` (внутри хранит CMRecordset)
- Для JSON: `JsonLoader<T>` (внутри хранит json-массив)
- Для моков: аналогично

### 3.4. Рефактор ContainerWrapper
- Теперь он не зависит от типа источника:
  ```cpp
  template<typename Container>
  class ContainerWrapper {
      Container& container;
      std::unique_ptr<ILoader> loader;
  public:
      ContainerWrapper(Container& cont, std::unique_ptr<ILoader> ldr)
          : container(cont), loader(std::move(ldr)) {}
      void LoadAll() {
          using ItemType = typename Container::value_type;
          while (loader->Next()) {
              ItemType item;
              loader->FillCurrent(&item);
              container.push_back(item);
          }
      }
  };
  ```

### 3.5. Реализуйте универсальный интерфейс базы данных
- Например, `MSDatabase` или `JSONDatabase`, которые принимают Loader и контейнер.

### 3.6. Пример использования
```cpp
// Для БД
CMRecordset rec(...);
auto loader = std::make_unique<RecordsetLoader<MyData>>(std::move(rec));
std::vector<MyData> results;
MSDatabase db;
db.Load(results, std::move(loader));

// Для JSON
nlohmann::json j = ...;
auto loader = std::make_unique<JsonLoader<MyData>>(j);
db.Load(results, std::move(loader));
```

---

## 4. Преимущества подхода
- **Гибкость:** легко добавлять новые источники данных.
- **Тестируемость:** можно использовать моки и json без реальной БД.
- **Изоляция:** бизнес-структуры не зависят от инфраструктуры.

---

## 5. Рекомендации
- Для новых структур всегда реализуйте универсальный `FromSource`.
- Для тестов используйте `JSONDatabase` или моки.
- Для интеграции с БД используйте `RecordsetLoader`.

---

## 6. Пример файловой структуры
```
src/
  ILoader.h
  RecordsetLoader.h
  JsonLoader.h
  IDatabase.h
  MSDatabase.h
  JSONDatabase.h
  example_usage.cpp
  example_json_db.cpp
```

---

**Документ подготовлен для проекта MERP.  
См. примеры в `C:/Monarch/MERP_UNIT_TEST/src/`.  
Вопросы и предложения — в команду архитектуры.**
