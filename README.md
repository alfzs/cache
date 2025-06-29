# Хранилище данных (Cache)

Пакет `Cache` предоставляет унифицированный интерфейс для работы с key-value хранилищем и очередями с двумя реализациями: in-memory и Redis.

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

### 1. In-Memory хранилище (`memoryCache`)

- Хранит данные в оперативной памяти
- Поддерживает автоматическую очистку устаревших записей (GC)
- Потокобезопасная реализация с использованием `sync.RWMutex`
- Не требует внешних зависимостей

**Инициализация:**

```go
cache, err := cache.NewMemory[T](cleanupInterval)
```

### 2. Redis хранилище (`redisCache`)

- Использует Redis в качестве бэкенда
- Поддерживает все основные операции Redis
- Автоматическая сериализация/десериализация данных в JSON
- Требует подключения к серверу Redis

**Инициализация:**

```go
config := cache.RedisConfig{
    Addr:     "localhost:6379",
    Username: "",
    Password: "",
    DB:       0,
}
cache, err := cache.NewRedis[T](config)
```

## Примеры использования

### Работа с ключами и значениями

```go
// Создание хранилища
cache, _ := cache.NewMemory[string](time.Minute)

// Сохранение значения
ctx := context.Background()
err := cache.Set(ctx, "key1", "value1", time.Minute)

// Получение значения
value, exists, err := cache.Get(ctx, "key1")

// Удаление значения
err = cache.Delete(ctx, "key1")
```

### Работа с очередями

```go
// Добавление в очередь
err := cache.Enqueue(ctx, "queue1", "item1")

// Извлечение из очереди
item, exists, err := cache.Dequeue(ctx, "queue1")

// Просмотр первого элемента
item, exists, err := cache.Peek(ctx, "queue1")

// Получение длины очереди
length, err := cache.QueueLen(ctx, "queue1")
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
