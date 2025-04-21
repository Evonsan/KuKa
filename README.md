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

