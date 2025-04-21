# KuKa
Кулинарный каталог
## Структура проекта
```
foodgram-project-react/
├── backend/         # Django-бэкенд
├── frontend/        # React-фронтенд
├── infra/           # Docker и конфигурации запуска
└── docs/            # Документация 
```

## Бэкенд (Django)
### Основные приложения
- `recipes/` — модели рецептов, ингредиентов, тегов, избранного, корзины.
- `users/` — пользовательская модель и подписки.
- `api/` — сериализаторы, viewset'ы и маршруты.

### Примеры моделей
```python
class Recipe(models.Model):
    name = models.CharField(max_length=200)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    ingredients = models.ManyToManyField(Ingredient, through='IngredientInRecipe')
    tags = models.ManyToManyField(Tag)
    image = models.ImageField(upload_to='recipes/')
    cooking_time = models.PositiveIntegerField()
```

```python
class Favorite(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    recipe = models.ForeignKey(Recipe, on_delete=models.CASCADE)
```

###  Основные API endpoint'ы
| Метод | URL | Назначение |
|-------|-----|------------|
| GET | `/api/recipes/` | Получить рецепты |
| POST | `/api/recipes/` | Создать рецепт |
| GET | `/api/tags/` | Получить теги |
| GET | `/api/ingredients/` | Получить ингредиенты |
| POST | `/api/users/subscribe/` | Подписка на пользователя |

## Фронтенд (React)
- Используется `React` + `React Router` + `axios`.
- Компоненты разбиты на папки: `Header`, `Main`, `Recipe`, `User`, `Auth`, `Cart`.

### Пример компонента:
```jsx
function RecipeCard({ recipe }) {
  return (
    <div className="card">
      <img src={recipe.image} alt={recipe.name} />
      <h2>{recipe.name}</h2>
      <p>Автор: {recipe.author}</p>
    </div>
  );
}
```

### Запрос к API:
```js
axios.get('/api/recipes/')
  .then(response => {
    setRecipes(response.data.results);
  });
```

## CI/CD и запуск
### Локальный запуск:
```bash
docker-compose up --build
```

### Используемые контейнеры:
- `backend` — Django + Gunicorn
- `frontend` — React
- `db` — PostgreSQL
- `nginx` — прокси-сервер


## Приложения к проекту
- `openapi-schema.yml` — документация по апи
- `redoc.html` — для опенапи схемы
- `docker-compose.yml`, `.env`, `README.md`
## Инструкция по деплою
* Установите docker на сервер:
```
sudo apt install docker.io 
```
* Установите docker-compose на сервер:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
* Локально отредактируйте файл infra/nginx.conf и в строке server_name впишите свой IP
* Скопируйте файлы docker-compose.yml и nginx.conf из директории infra на сервер:
```
scp docker-compose.yml <username>@<host>:/home/<username>/docker-compose.yml
scp nginx.conf <username>@<host>:/home/<username>/nginx.conf
```

* Cоздайте .env файл и впишите:
    ```
    DB_ENGINE=<django.db.backends.postgresql>
    DB_NAME=<имя базы данных postgres>
    DB_USER=<пользователь бд>
    DB_PASSWORD=<пароль>
    DB_HOST=<db>
    DB_PORT=<5432>
    ```

* На сервере соберите docker-compose:
```
sudo docker-compose up -d --build
```
* После успешной сборки на сервере выполните команды (только после первого деплоя):
    - Выполните миграцию:
    ```
    sudo docker-compose exec backend python manage.py makemigrations
    ```
    - Примените миграции:
    ```
    sudo docker-compose exec backend python manage.py migrate --noinput
    ```
    - Соберите статические файлы:
    ```
    sudo docker-compose exec backend python manage.py collectstatic --noinput
    ```
    - Создать суперпользователя Django:
    ```
    sudo docker-compose exec backend python manage.py createsuperuser
    ```
    - Проект будет доступен по вашему IP
