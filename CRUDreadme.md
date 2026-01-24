# Практика CRUD приложение

Валидация полей.\
Настройка датагрида.\
Вывод кол-ва записей в таблице.\
Испорт и экспорт бд.\
Тесты для приложения.\
## Обработка.
Вывод если коллекция пустая.\

## Шаг 1. Основа MVVM приложения
Создаем архитектуру MVVM приложения.\
```
StartupUri="Views/MainWindow.xaml"
```
Делаем разметку окна.\
Настраиваем датагрид.\
Делаем вью-модель для окна.\
Присоединяем вью-модель ко вью.\
### Создание команд
Создаем папку Infrastructure. В ней папку Commands. В ней папку Base. В ней создаем класс Command.
```
using System;
using System.Windows.Input;

namespace WpfApp1.Infrastructure.Commands.Base
{
    internal abstract class Command : ICommand
    {
        public event EventHandler CanExecuteChanged
        {
            add => CommandManager.RequerySuggested += value;
            remove => CommandManager.RequerySuggested -= value;
        }

        public abstract bool CanExecute(object parameter);

        public abstract void Execute(object parameter);
    }
}
```
На основании него создаем класс LambdaCommand.
```
using System;
using WpfApp1.Infrastructure.Commands.Base;

namespace WpfApp1.Infrastructure.Commands
{
    internal class LambdaCommand : Command
    {
        private readonly Action<object> _Execute;

        private readonly Func<object, bool> _CanExecute;

        public LambdaCommand(Action<object> Execute, Func<object, bool> CanExecute = null)
        {
            _Execute = Execute ?? throw new ArgumentNullException(nameof(Execute));
            _CanExecute = CanExecute;
        }
        public override bool CanExecute(object parameter) => _CanExecute?.Invoke(parameter) ?? true;

        public override void Execute(object parameter) => _Execute(parameter);
    }
}
```

## Шаг 2. Выгрузка данных

## Шаг 3. Добавление элемента
Теперь начинаем реализовывать CRUD функции.\
Создаем объект во вью-модели.\
Делаем модель Product вью-моделью.\
С помощью Behaviors делаем добавление.\
Создаем объект команды и методы для него во вью-модели.
```
public ICommand AddProductCommand { get; }

private bool CanAddProductCommandExecute(object p) => true;

private void OnAddProductCommandExecuted(object p)
{
    
}
```
Создаем команду в конструкторе.
```
AddProductCommand = new LambdaCommand(OnAddProductCommandExecuted, CanAddProductCommandExecute);
```
Прописываем установку Idшника и рессет значений для объекта NewProduct.\

## Шаг 3. Копирование элемента

## Шаг 4. Редактирование элемента

## Шаг 5. Удаление элемента

## Шаг 6. Множественное удаление

## Шаг ?. Недоступность кнопок копирования, редактирования, очищение полей редактирования в зависимости от кол-ва выделенных элементов в списке

## Шаг ?. Цвет результата операции
Создаем enum.
```
namespace WpfApp1.Models.Enums
{
    internal enum OperationResultTitle
    {
        Default,
        DataSuccessfullyUploaded, //Данные успешно выгружены
        DataNotSuccessfullyUploaded, //Не удалось выгрузить данные
        NewObjectSuccessfullyAdded, //Новый объект успешно добавлен
        NewObjectNotSuccessfullyAdded, //Не удалось добавить новый объект
        DataSuccessfullySaved, //Данные успешно сохранены
        DataNotSuccessfullySaved, //Не удалось сохранить данные
        ObjectSuccessfullyDeleted, //Объект успешно удален
        ObjectNotSuccessfullyDeleted, //Не удалось удалить объект
        ObjectsSuccessfullyDeleted, //Объекты успешно удалены
        ObjectsNotSuccessfullyDeleted, //Не удалось удалить объекты
    }
}

```
Создаем класс OperationResult, который будет хранить результат операции и ее успешность.
```
using WpfApp1.Models.Enums;

namespace WpfApp1.Models
{
    internal class OperationResult
    {
        public OperationResultTitle OperationResultTitle { get; set; }

        public bool IsSuccessful { get; set; }
    }
}
```
Пишем два конвертера.\
Один для конвентации enum'а в строку.
```
using System;
using System.Globalization;
using WpfApp1.Infrastructure.Converters.Base;
using WpfApp1.Models.Enums;

namespace WpfApp1.Infrastructure.Converters
{
    internal class OperationResultTitleToStringConverter : Converter
    {
        public override object Convert(object v, Type t, object p, CultureInfo c)
        {
            if (v is null) return null;

            switch ((OperationResultTitle)v)
            {
                case OperationResultTitle.Default:
                    return string.Empty;
                case OperationResultTitle.DataSuccessfullyUploaded:
                    return "Данные успешно выгружены";
                case OperationResultTitle.DataNotSuccessfullyUploaded:
                    return "Не удалось выгрузить данные";
                case OperationResultTitle.NewObjectSuccessfullyAdded:
                    return "Новый объект успешно добавлен";
                case OperationResultTitle.NewObjectNotSuccessfullyAdded:
                    return "Не удалось добавить новый объект";
                case OperationResultTitle.DataSuccessfullySaved:
                    return "Данные успешно сохранены";
                case OperationResultTitle.DataNotSuccessfullySaved:
                    return "Не удалось сохранить данные";
                case OperationResultTitle.ObjectSuccessfullyDeleted:
                    return "Объект успешно удален";
                case OperationResultTitle.ObjectNotSuccessfullyDeleted:
                    return "Не удалось удалить объект";
                case OperationResultTitle.ObjectsSuccessfullyDeleted:
                    return "Объекты успешно удалены";
                case OperationResultTitle.ObjectsNotSuccessfullyDeleted:
                    return "Не удалось удалить объекты";
                default:
                    return string.Empty;
            }
        }
    }
}
```
Другой для перевода успешности результата в цвет.
```
using System;
using System.Globalization;
using System.Windows.Media;
using WpfApp1.Infrastructure.Converters.Base;

namespace WpfApp1.Infrastructure.Converters
{
    internal class OperationResultIsSuccessfulToSolidColorBrushConverter : Converter
    {
        public override object Convert(object v, Type t, object p, CultureInfo c)
        {
            if (v is null) return null;

            switch ((bool)v)
            {
                case true:
                    return new SolidColorBrush(Colors.Green);
                case false:
                    return new SolidColorBrush(Colors.Red);
                default:
                    return new SolidColorBrush(Colors.Transparent);
            }
        }
    }
}
```
Подключаем пространство имен конверторов в разметке.
```
xmlns:c="clr-namespace:WpfApp1.Infrastructure.Converters"
```
В ресурсах создаем конвертеры.
```
<Window.Resources>
    <c:OperationResultToStringConverter x:Key="OperationResultToStringConverter"/>
    <c:OperationResultToSolidColorBrushConverter x:Key="OperationResultToSolidColorBrushConverter"/>
</Window.Resources>
```
Используем конверторы в привязке.
```
<TextBlock DockPanel.Dock="Top"
           Text="{Binding OperationResult.OperationResultTitle, Converter={StaticResource OperationResultToStringConverter}}"
           Foreground="{Binding Path=OperationResult.IsSuccessful, Converter={StaticResource OperationResultToSolidColorBrushConverter}}"/>
```
<TextBlock DockPanel.Dock="Top" Text="{Binding OperationResult.OperationResultTitle}"/>

## Шаг ?. Валидация полей

## Описание
Связь событий и команд реализуется при помощи Behaviors.\
