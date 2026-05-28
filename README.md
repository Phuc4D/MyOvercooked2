# MyCookingGame

> A 2D cooking simulation game inspired by Overcooked — built with Unity 6 & C#

![Unity](https://img.shields.io/badge/Unity-6-black?logo=unity)
![Language](https://img.shields.io/badge/C%23-UniTask%20%7C%20VContainer-239120?logo=csharp)
![Platform](https://img.shields.io/badge/Platform-PC%20%7C%20WebGL-blue)
![Status](https://img.shields.io/badge/Status-In%20Development-yellow)

**[▶ Play in Browser](https://phuc4d.itch.io/mycookinggamee)** · **[View Source](https://github.com/Phuc4D/MyCookingGame)**

---

## 📸 Gameplay

> 🎬 *GIF coming soon — record with ScreenToGif and drop here*

---

## 🎮 About

MyCookingGame is a single-player cooking sim where players chop ingredients, cook meals, and serve impatient customers before time runs out. The project focuses on clean data-driven architecture and async AI design rather than feature breadth.

---

## 🏗️ Architecture Highlights

### Three-Layer ScriptableObject Food System
The food pipeline is driven entirely by data assets — no hardcoded ingredient logic anywhere in the codebase.

| SO Asset | Responsibility |
|---|---|
| `FoodStateSO` | Defines an ingredient state (raw, chopped, cooked) |
| `TransformationSO` | Encodes processing rules: input + station → output |
| `RecipeSO` | Defines a customer order and its score value |
| `RecipeDatabaseSO` | Central lookup for all transformations and recipes |

**Why this matters:** Adding a new ingredient requires zero code changes — just create new `.asset` files. Station logic stays generic via `TryGetTransformation()` queries.

```csharp
// O(1) recipe validation — no iteration, no hashing
if (food.CurrentData == _recipe.finalMeal)
    CompleteOrder();
```

### Customer AI — Async Lifecycle
Customer behavior is modeled as a sequential async script rather than an Update()-based state machine, eliminating per-frame polling entirely.

```csharp
private async UniTaskVoid RunLifecycleAsync(CancellationToken ct)
{
    await WalkToAsync(_waitingSpot.transform.position, ct);
    bool served = await WaitForServiceAsync(ct);
    _animator.SetBool(served ? "IsJoyful" : "IsAngry", true);
    await UniTask.Delay(TimeSpan.FromSeconds(1.5f), ct);
    await WalkToAsync(_exitPoint, ct);
    Destroy(gameObject);
}
```

### Dual Station Interaction Models
- **Hold-to-progress** (Chopping) — progress scales with held duration
- **Time-elapsed** (Cooking) — progress runs automatically on a timer

Both models share a single reusable circular Progress UI component — timing logic is fully decoupled from visual feedback.

---

## 🛠️ Tech Stack

| Category | Technology |
|---|---|
| Engine | Unity 6 |
| Language | C# |
| Async | UniTask |
| Dependency Injection | VContainer |
| Version Control | Git |

---

## 📁 Project Structure

```
Assets/
├── Scripts/
│   ├── Customer/       # AI lifecycle, patience system
│   ├── Food/           # FoodItem, FoodStateSO, TransformationSO
│   ├── Recipe/         # RecipeSO, RecipeDatabaseSO
│   ├── Station/        # InstantStation, TimedStation, BaseStation
│   └── UI/             # Progress UI, HUD
├── ScriptableObjects/
│   ├── Foods/          # All FoodStateSO assets
│   ├── Transformations/# All TransformationSO assets
│   └── Recipes/        # All RecipeSO assets
└── Prefabs/
```

---

## 🚀 Getting Started

```bash
# Clone the repo
git clone https://github.com/Phuc4D/MyCookingGame.git

# Open in Unity Hub
# Requires Unity 6.x
# Install packages: UniTask, VContainer (via Package Manager)
```

---

## 👤 Author

**Nguyen Phuoc Hong Phuc**
[github.com/Phuc4D](https://github.com/Phuc4D) · [nphphucdev@gmail.com](mailto:nphphucdev@gmail.com)
