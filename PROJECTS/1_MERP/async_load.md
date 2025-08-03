# Асинхронная операция-заглушка в CSupplierOrderLineDlg

Этот документ описывает механизм работы тестовой асинхронной операции (заглушки), реализованной в диалоге `CSupplierOrderLineDlg` в проекте MERP. Цель этой заглушки - проверить базовую инфраструктуру для выполнения фоновых задач без блокировки основного потока пользовательского интерфейса (UI).

## Компоненты механизма

1.  **`CSupplierOrderLineDlg.h`**:
    *   `std::unique_ptr<std::thread> m_upPlaceholderAsyncThread;`: Умный указатель для управления объектом потока. Автоматически освобождает ресурсы.
    *   `bool m_bStopPlaceholderAsyncThread;`: Флаг (временно `bool` вместо `std::atomic<bool>` из-за проблем с линтером) для сигнализации рабочему потоку о необходимости завершения. Инициализируется в `false` в конструкторе диалога.
    *   `PlaceholderAsyncThreadProc()`: Приватный метод класса, который будет выполняться в отдельном потоке.
    *   `StopAndJoinPlaceholderAsyncThread()`: Приватный метод для корректной остановки и ожидания завершения рабочего потока.
    *   `WM_APP_PLACEHOLDER_COMPLETE`: Статическая константа, определяющая идентификатор пользовательского сообщения Windows. Используется для уведомления основного потока о завершении асинхронной операции.
    *   `OnPlaceholderAsyncComplete(WPARAM wParam, LPARAM lParam)`: Метод-обработчик сообщения `WM_APP_PLACEHOLDER_COMPLETE`. Выполняется в основном потоке UI.

2.  **`CSupplierOrderLineDlg.cpp`**:
    *   **Конструктор `CSupplierOrderLineDlg(...)`**: Инициализирует `m_bStopPlaceholderAsyncThread = false;`.
    *   **`BEGIN_MESSAGE_MAP(...) ... END_MESSAGE_MAP()`**: Карта сообщений диалога, содержит макрос `ON_MESSAGE(WM_APP_PLACEHOLDER_COMPLETE, &CSupplierOrderLineDlg::OnPlaceholderAsyncComplete)`, связывающий пользовательское сообщение с его обработчиком.
    *   **`OnInitDialog()`**:
        *   Сбрасывает `m_bStopPlaceholderAsyncThread = false;`.
        *   Создает и запускает новый поток: `m_upPlaceholderAsyncThread = std::make_unique<std::thread>(&CSupplierOrderLineDlg::PlaceholderAsyncThreadProc, this);`. Поток начинает выполнение функции `PlaceholderAsyncThreadProc`.
    *   **`PlaceholderAsyncThreadProc()`**:
        *   Выполняет цикл (в данном примере 5 итераций).
        *   На каждой итерации проверяет флаг `m_bStopPlaceholderAsyncThread`. Если `true`, поток завершает свою работу (делает `return`).
        *   Имитирует работу с помощью `std::this_thread::sleep_for(std::chrono::milliseconds(200));`.
        *   После цикла, если флаг `m_bStopPlaceholderAsyncThread` все еще `false`, отправляет сообщение `WM_APP_PLACEHOLDER_COMPLETE` в очередь сообщений основного потока с помощью `PostMessage(WM_APP_PLACEHOLDER_COMPLETE);`.
    *   **`OnPlaceholderAsyncComplete(WPARAM wParam, LPARAM lParam)`**:
        *   Выполняется, когда основной поток извлекает и обрабатывает сообщение `WM_APP_PLACEHOLDER_COMPLETE`.
        *   В данном примере просто отображает `AfxMessageBox` с уведомлением о завершении.
    *   **`StopAndJoinPlaceholderAsyncThread()`**:
        *   Проверяет, был ли создан поток (`if (m_upPlaceholderAsyncThread)`).
        *   Устанавливает `m_bStopPlaceholderAsyncThread = true;`, давая команду рабочему потоку на завершение.
        *   Проверяет, можно ли присоединиться к потоку (`m_upPlaceholderAsyncThread->joinable()`).
        *   Вызывает `m_upPlaceholderAsyncThread->join();`, что блокирует вызывающий поток (в данном случае основной поток при закрытии диалога) до тех пор, пока рабочий поток `PlaceholderAsyncThreadProc` полностью не завершится.
        *   Сбрасывает умный указатель `m_upPlaceholderAsyncThread.reset();`.
    *   **`OnOK()` и `OnCancel()`**:
        *   Перед вызовом базового метода `CMDialogEx::OnOK()` или `CMDialogEx::OnCancel()` вызывают `StopAndJoinPlaceholderAsyncThread()`, чтобы гарантировать корректное завершение фонового потока перед закрытием диалога.

## Схема взаимодействия

```mermaid
sequenceDiagram
    participant UIThread as Основной поток (UI)
    participant WorkerThread as Рабочий поток (PlaceholderAsyncThreadProc)

    UIThread->>+CSupplierOrderLineDlg: OnInitDialog()
    Note over UIThread: m_bStopPlaceholderAsyncThread = false;
    UIThread->>+WorkerThread: std::thread(&CSupplierOrderLineDlg::PlaceholderAsyncThreadProc, this)
    Note over WorkerThread: Начинает выполнение PlaceholderAsyncThreadProc()

    loop 5 раз (имитация работы)
        WorkerThread-->>WorkerThread: Проверка m_bStopPlaceholderAsyncThread
        alt m_bStopPlaceholderAsyncThread == true
            Note over WorkerThread: Флаг остановки установлен, завершение.
            WorkerThread-->>UIThread: Поток завершается (return)
            deactivate WorkerThread
        end
        WorkerThread-->>WorkerThread: std::this_thread::sleep_for(200ms)
    end

    opt WorkerThread is active
        Note over WorkerThread: Цикл завершен
        WorkerThread-->>WorkerThread: Проверка m_bStopPlaceholderAsyncThread
        alt m_bStopPlaceholderAsyncThread == false
            WorkerThread-->>UIThread: PostMessage(WM_APP_PLACEHOLDER_COMPLETE)
        end
        Note over WorkerThread: Завершение своей работы.
    end
    
    UIThread->>UIThread: Обработка очереди сообщений
    opt Сообщение WM_APP_PLACEHOLDER_COMPLETE получено
        UIThread->>CSupplierOrderLineDlg: OnPlaceholderAsyncComplete()
        Note over UIThread: AfxMessageBox("Завершено!")
    end

    alt Диалог закрывается (OnOK/OnCancel)
        UIThread->>CSupplierOrderLineDlg: OnOK() или OnCancel()
        UIThread->>CSupplierOrderLineDlg: StopAndJoinPlaceholderAsyncThread()
        Note over UIThread: m_bStopPlaceholderAsyncThread = true;
        opt WorkerThread is active and joinable
            UIThread->>WorkerThread: Ожидание (join())
            WorkerThread-->>UIThread: Рабочий поток завершен (после join)
            deactivate WorkerThread
        end
        Note over UIThread: m_upPlaceholderAsyncThread.reset();
        UIThread->>CSupplierOrderLineDlg: CMDialogEx::OnOK() / OnCancel()
    end
```

## Поток выполнения

1.  **Инициализация диалога (`OnInitDialog`)**:
    *   Основной поток UI запускает новый "рабочий" поток, который начинает выполнять `PlaceholderAsyncThreadProc`.
    *   Основной поток продолжает свою работу, не ожидая завершения рабочего потока (UI остается отзывчивым).

2.  **Работа фонового потока (`PlaceholderAsyncThreadProc`)**:
    *   Рабочий поток выполняет свою "задачу" (в данном случае, цикл с задержками).
    *   Периодически проверяет флаг `m_bStopPlaceholderAsyncThread`. Если он `true`, поток немедленно завершается.
    *   По завершении своей основной задачи (если не был остановлен раньше), рабочий поток отправляет сообщение `WM_APP_PLACEHOLDER_COMPLETE` в очередь сообщений основного потока UI.
    *   После отправки сообщения (или если был остановлен) рабочий поток завершает свое выполнение.

3.  **Обработка завершения в основном потоке (`OnPlaceholderAsyncComplete`)**:
    *   Основной поток UI в своем цикле обработки сообщений получает `WM_APP_PLACEHOLDER_COMPLETE`.
    *   Вызывается метод `OnPlaceholderAsyncComplete`. В нем можно безопасно обновить элементы UI или выполнить другие действия, которые должны произойти после завершения фоновой задачи.

4.  **Закрытие диалога (`OnOK`/`OnCancel`)**:
    *   Основной поток UI вызывает `StopAndJoinPlaceholderAsyncThread`.
    *   Устанавливается флаг `m_bStopPlaceholderAsyncThread = true;`. Это важно, если рабочий поток еще не завершился – он увидит этот флаг и прекратит свою работу.
    *   Вызывается `m_upPlaceholderAsyncThread->join()`. Это заставляет основной поток UI дождаться, пока рабочий поток действительно завершит свое выполнение. Это предотвращает ситуацию, когда диалог и его члены (включая `this`, используемый в потоке) уничтожаются до того, как рабочий поток закончит свою работу, что могло бы привести к падению.
    *   После того как `join()` вернет управление, ресурсы потока освобождаются, и диалог закрывается.

Этот механизм обеспечивает базовую асинхронность, позволяя выполнять длительные операции в фоне и уведомлять основной поток о их завершении для последующей обработки результатов или обновления UI.