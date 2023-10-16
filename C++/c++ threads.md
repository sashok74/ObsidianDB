Пример кода на C++ с использованием многопоточности:

```c++
#include <iostream>
#include <thread> // Для использования потоков

void threadFunction() // Функция, которую выполнит поток
{
std::cout << \"Hello from thread \" << std::this_thread::get_id() << std::endl;\n}\n\nint main()\n{\n    std::cout << \"Main thread \" << std::this_thread::get_id() << std::endl;\n\n    std::thread t1(threadFunction); // Создание нового потока\n    std::thread t2(threadFunction);\n\n    t1.join(); // Ожидание завершения первого потока\n    t2.join(); // Ожидание завершения второго потока\n\n    std::cout << \"Threads have finished\" << std::endl;\n\n    return 0;\n}\n```

В этом примере мы создаем два потока, вызывая функцию `threadFunction()`. Затем мы используем метод `join()` для ожидания завершения выполнения каждого потока. По завершении работы всех потоков, программа продолжается выполняться в главном потоке.