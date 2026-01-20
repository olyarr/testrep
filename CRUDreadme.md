# Практика CRUD приложение

Валидация полей.\
Настройка датагрида.\
Вывод кол-ва записей в таблице.\
Испорт и экспорт бд.\
Тесты для приложения.\
## Обработка.
Вывод если коллекция пустая.\

## Шаг 1.
Создаем архитектуру MVVM приложения.\
```
StartupUri="Views/MainWindow.xaml"
```
Делаем разметку окна.\
Делаем вью-модель для окна.\
Присоединяем вью-модель ко вью.\

## Шаг 2. Добавление элемента
Теперь начинаем реализовывать CRUD функции.\
Создаем объект во вью-модели.\
Делаем модель Product вью-моделью.\
С помощью Behaviors делаем добавление.\
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

Связь событий и команд реализуется при помощи Behaviors.\
