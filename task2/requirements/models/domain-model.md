# Domain Model – MealSwipe

## UML Conceptual Class Diagram

```mermaid
classDiagram
    class UserProfile {
        goals : List~String~
        hardFilters : List~String~
        budget : decimal
        calorieTarget : int
        proteinTarget : float
    }

    class SwipeSession {
    }

    class Swipe {
        liked : boolean
    }

    class Recipe {
        name : String
        kcal : int
        protein : float
        price : decimal
        cookingTime : int
    }

    class HealthRating {
        rating : String
    }

    class MealPlan {
        mealCount : int
    }

    class ShoppingList {
        items : List~String~
        totalPrice : decimal
    }

    UserProfile "1" -- "0..*" SwipeSession : starts
    SwipeSession "1" -- "0..*" Swipe : contains
    Swipe "0..*" -- "1" Recipe : refers to
    UserProfile "1" -- "0..*" HealthRating : receives
    Recipe "1" -- "0..*" HealthRating : has
    SwipeSession "1" -- "0..*" MealPlan : generates
    MealPlan "0..*" -- "1..*" Recipe : includes
    MealPlan "1" -- "1" ShoppingList : has
```

## Domain Glossary

| Domain Concept | Definition |
|---|---|
| UserProfile | Represents the user's personal goals, dietary filters, budget, and nutritional targets. |
| SwipeSession | A session in which the user expresses preferences for recipes. |
| Swipe | A user's decision to like or dislike a recipe. |
| Recipe | A dish with nutritional information, price, and cooking time. |
| HealthRating | An evaluation of a recipe relative to the user's personal goals. |
| MealPlan | A selection of liked recipes based on price, cooking time, and personal goals. |
| ShoppingList | An aggregated list of items required for a meal plan, including pricing information. |

## Domain Constraints

- Recipes must comply with the user's hard dietary filters.
- A meal plan consists of recipes liked by the user.
- A meal plan contains the requested number of meals (X).
- Meal selection considers price, cooking time, and personal goals.
- Health ratings depend on the recipe and the user's goals.
- The shopping list is aggregated from the selected recipes.
- Previous meal selections are considered to support variety.
