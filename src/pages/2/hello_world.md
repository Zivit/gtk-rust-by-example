# Окно с панелью заголовка

<img src="img/hello_world.png" />

> Исходный код программы для этой главы можно найти [здесь](https://github.com/ruRust/gtk-rust-by-example/tree/master/src/source_code/hello_world).

В этой главе мы создадим простое GTK приложение с панелью заголовка. Этот пример познакомит Вас с основами создания структуры приложения, которое будет содержать виджет и окно с заголовком.

## Создание структуры приложения

Следующая диаграмма показывает, как мы будет проектировать структуру для нашего GTK приложения.
Можно и не следовать этой диаграмме для достижения главного результата, однако, проектирование структуры пользовательского интерфейса может быть полезной пищей для ума.

<img src="img/diagram.png" />

В примере ниже, мы создадим структуру с **GtkWindow** и **Header**:

```rust
extern crate gtk;

use gtk::*;

// Объявим структуру `Application`.
pub struct Application {
    pub window: Window,
    pub header: Header,
}
```

**Header** - это еще одна структура, которая будет содержать **GtkHeaderbar** и все виджеты внутри этой панели заголовка.

```rust
// Объявим структуру `Header`.
pub struct Header {
    pub container: HeaderBar,
}
``` 

## Создание пользовательского интерфейса (UI)

Далее, мы создадим интерфейс с помощью Rust и расположим его в "ново-объявленных" структурах.

Во-первых, структура нашего приложения будет содержать в себе все другие структуры нашего пользовательского интерфейса. В примере ниже находится код с комментариями, описывающие каждый метод, который мы будем использовать для конфигурации.

Мы создадим **GtkWindow** и "присоединим" каждый элемент UI. Структура `Header` будет распологать в себе другие **GtkHeaderBar**. Далее, напишем функцию для выхода из приложения.
Важно также установить описание для окна и `wmclass`, который будет отображаться оконными менеджерами, а с помощью функции `Window::set_default_icon_name()` настроим отображаемую иконку.

```rust
// Блок реализации.
impl Application {
    fn new() -> Application {
        // Создадим новое окно с типом `Toplevel`.
        let window = Window::new(WindowType::Toplevel);
        // Создадим панель заголовка и связанный с ним контент.
        let header = Header::new();

        // Укажем название заголовка виджета.
        window.set_titlebar(&header.container);
        // Укажем название для окна приложения.
        window.set_title("Простая программа");
        // Установим класс для оконного менеджера.
        window.set_wmclass("simple-gtk", "Простая программа");
        // Установим иконку, отображаемую приложением.
        Window::set_default_icon_name("имя_иконки");

        // Программа закроется, если нажата кнопка выхода.
        window.connect_delete_event(move |_, _| {
            main_quit();
            Inhibit(false)
        });

        // Возвращаем основное состояние приложения.
        Application { window, header }
    }
}
```
Здесь распологается блок реализации для структуры **Header**, которая на данный момент содержит только **GtkHeaderBar**. Важно указать описание для этой панели заголовка, чтобы она отображалась, а также сделать активными элементы управления окном, поскольку они отключены по-умолчанию.

```rust
impl Header {
    fn new() -> Header {
        // Создадим виджет контейнера для главной панели заголовка.
        let container = HeaderBar::new();
        // Установим отображаемый тект в секции для названия.
        container.set_title("Simple GTK");
        // Делаем активными элементы управления окна в этой панели.
        container.set_show_close_button(true);

        // Возвращаем заголовок и его состояние.
        Header { container }
    }
}
```
## Инициализация и запуск приложения

Теперь, когда мы готовы, нам нужно просто инициализировать GTK, создать структуру приложения, показать все виджеты внутри этой структуры и запустить главный цикл событий GTK.

```rust
fn main() {
    // Инициализация GTK.
    if gtk::init().is_err() {
        eprintln!("Не удалось инициализировать GTK приложение.");
        return;
    }

    // Инициализация начального состояния UI.
    let app = Application::new();

    // Делаем видимыми все виджеты с UI.
    app.window.show_all();

    // Запуск основного цикла GTK.
    gtk::main();
}
```

После того, как основной поток вошёл в цикл событий, он будет взаимодействовать с каждым вызваным виджетом для действий, например: метод `connect_delete_event()`, который мы использовали выше, чтобы запрограммировать кнопку выхода для закрытия программы.

## Результат

После того, как вы написали код, соберите и запустите программу, которая выглядит вот так:
```bash
cargo build && cargo run
```

<img src="img/hello_world.png" />