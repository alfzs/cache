# Хранилище данных (Storage)

Пакет `storage` предоставляет унифицированный интерфейс для работы с key-value хранилищем и очередями с двумя реализациями: in-memory и Redis.

## Возможности

- **Key-value операции**:

  - `Set` - сохранение значения по ключу с TTL
  - `Get` - получение значения по ключу
  - `Delete` - удаление значения по ключу

- **Очереди**:
  - `Enqueue` - добавление элемента в конец очереди
  - `Dequeue` - извлечение и удаление элемента из начала очереди
  - `Peek` - просмотр элемента в начале очереди без удаления
  - `Remove` - удаление элемента из начала очереди без возврата
  - `QueueLen` - получение длины очереди

## Реализации

### 1. In-Memory хранилище (`memoryStorage`)

- Хранит данные в оперативной памяти
- Поддерживает автоматическую очистку устаревших записей (GC)
- Потокобезопасная реализация с использованием `sync.RWMutex`
- Не требует внешних зависимостей

**Инициализация:**

```go
storage, err := storage.NewMemory[T](cleanupInterval)
```

### 2. Redis хранилище (`redisStorage`)

- Использует Redis в качестве бэкенда
- Поддерживает все основные операции Redis
- Автоматическая сериализация/десериализация данных в JSON
- Требует подключения к серверу Redis

**Инициализация:**

```go
config := storage.RedisConfig{
    Addr:     "localhost:6379",
    Username: "",
    Password: "",
    DB:       0,
}
storage, err := storage.NewRedis[T](config)
```

## Примеры использования

### Работа с ключами и значениями

```go
// Создание хранилища
storage, _ := storage.NewMemory[string](time.Minute)

// Сохранение значения
ctx := context.Background()
err := storage.Set(ctx, "key1", "value1", time.Minute)

// Получение значения
value, exists, err := storage.Get(ctx, "key1")

// Удаление значения
err = storage.Delete(ctx, "key1")
```

### Работа с очередями

```go
// Добавление в очередь
err := storage.Enqueue(ctx, "queue1", "item1")

// Извлечение из очереди
item, exists, err := storage.Dequeue(ctx, "queue1")

// Просмотр первого элемента
item, exists, err := storage.Peek(ctx, "queue1")

// Получение длины очереди
length, err := storage.QueueLen(ctx, "queue1")
```

## Требования

- Go 1.18+ (из-за использования generics)
- Для Redis реализации требуется [go-redis](https://github.com/redis/go-redis)

## Установка

```bash
go get github.com/alfzs/cache
```

## Особенности

- Обе реализации поддерживают контексты для управления временем выполнения операций
- Все методы потокобезопасны
- In-memory реализация автоматически удаляет устаревшие записи
- Redis реализация использует JSON для сериализации сложных типов

## Лицензия

[MIT](LICENSE)
