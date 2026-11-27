# Domain Model – MealSwipe

## UML Conceptual Class Diagram

```mermaid
classDiagram
    class UserProfile {
        goals : List~String~
        dietaryExclusions : List~String~
        calorieTargetKcal : decimal [0..1]
        proteinTargetG : decimal [0..1]
    }

    class SwipeSession

    class Swipe {
        liked : boolean
    }

    class Recipe {
        id : String
        name : String
        instructions : String
        originalServings : int
        cookingTimeMinutes : int
        kcal : decimal
        proteinG : decimal
        dietaryTags : List~String~
        allergens : List~String~
    }

    class RecipeProvider {
        name : String
    }

    class RecipeItem {
        quantity : decimal
    }

    class Ingredient {
        name : String
    }

    class Retailer {
        name : String
    }

    class IngredientPrice {
        unitPriceEur : decimal
    }

    class HealthRating {
        rating: String
    }

    class MealPlan {
        mealCount : int
        servings : int
        budgetEur : decimal [0..1]
    }

    class ShoppingList

    class ShoppingListItem {
        quantity : decimal
        unitPriceEur : decimal [0..1]
    }

    UserProfile "1" --> "0..*" SwipeSession : starts
    SwipeSession "1" *-- "0..*" Swipe : contains
    Swipe "0..*" --> "1" Recipe : refers to

    Recipe "0..*" --> "0..1" RecipeProvider : sourced from
    Recipe "1" *-- "1..*" RecipeItem : contains
    RecipeItem "0..*" --> "1" Ingredient : specifies

    Retailer "0..1" -- "0..*" IngredientPrice : provides
    IngredientPrice "0..*" --> "1" Ingredient : prices

    UserProfile "1" -- "0..*" HealthRating : receives
    HealthRating "0..*" --> "1" Recipe : evaluates

    SwipeSession "1" --> "0..*" MealPlan : produces
    MealPlan "0..*" o-- "1..14" Recipe : includes
    MealPlan "1" *-- "1" ShoppingList : has
    ShoppingList "1" *-- "1..*" ShoppingListItem : contains
    ShoppingListItem "0..*" --> "1" Ingredient : lists
```

## Domain Glossary

| Domain Concept | Definition |
|---|---|
| `UserProfile` | The user's goals, dietary exclusions, and optional default nutrition targets. |
| `SwipeSession` | A meal discovery session in which a user expresses preferences. |
| `Swipe` | A like or dislike for a recipe in a swipe session. |
| `Recipe` | A dish with ingredients, preparation instructions, and nutritional information. |
| `RecipeProvider` | A source that supplies recipe content, such as a recipe platform. |
| `RecipeItem` | The quantity of an ingredient specified in a particular recipe. |
| `Ingredient` | A food item that can be referenced by recipes, prices, and shopping lists. |
| `Retailer` | A grocery seller associated with ingredient prices. |
| `IngredientPrice` | A catalog price in EUR per standard unit of an ingredient, optionally attributed to a retailer. |
| `HealthRating` | An evaluation of a recipe's calories and protein against the user's active targets, with an explanation. |
| `MealPlan` | A selection of liked, eligible recipes for the requested number of meals. |
| `ShoppingList` | The combined ingredient requirements for a meal plan. Its estimated total cost is calculated from the list items. |
| `ShoppingListItem` | A combined quantity for an ingredient, with the unit price used for the estimate when available. |

## Domain Constraints

- Recipes must comply with the user's hard dietary filters.
- A meal plan consists of recipes liked by the user.
- A meal plan contains the requested number of meals (X).
- Meal selection considers price, cooking time, and personal goals.
- Health ratings depend on the recipe and the user's goals.
- Quantities use grams for weight, millilitres for volume, and pieces for counts. For example, 1.25 kg is represented as `quantity = 1250`.
- The shopping list combines quantities of the same ingredient expressed in the same standard unit.
- The estimated shopping-list total uses each item's quantity and a catalog price in EUR per the same unit. A complete estimate requires a price for every item.
- Previous meal selections are considered to support variety.
