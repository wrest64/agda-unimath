# Loop spaces

```agda
module synthetic-homotopy-theory.loop-spaces where
```

<details><summary>Imports</summary>

```agda
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.identity-types
open import foundation.universe-levels

open import structured-types.h-spaces
open import structured-types.magmas
open import structured-types.pointed-equivalences
open import structured-types.pointed-types
open import structured-types.wild-quasigroups
```

</details>

## Idea

The loop space of a pointed type `A` is the pointed type of self-identifications
of the base point of `A`. The loop space comes equipped with a group structure
induced by the groupoidal structure on identifications.

## List of files directly related to loop spaces

| Concept                      | File                                                                                                              |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Higher groups                | [`higher-group-theory.higher-groups`](higher-group-theory.higher-groups.md)                                       |
| Conjugation of loops         | [`synthetic-homotopy-theory.conjugation-loops`](synthetic-homotopy-theory.conjugation-loops.md)                   |
| Double loop spaces           | [`synthetic-homotopy-theory.double-loop-spaces`](synthetic-homotopy-theory.double-loop-spaces.md)                 |
| The Eckmann-Hilton argument  | [`synthetic-homotopy-theory.eckmann-hilton-argument`](synthetic-homotopy-theory.eckmann-hilton-argument.md)       |
| Free loops                   | [`synthetic-homotopy-theory.free-loops`](synthetic-homotopy-theory.free-loops.md)                                 |
| Functoriality of loop spaces | [`synthetic-homotopy-theory.functoriality-loop-spaces`](synthetic-homotopy-theory.functoriality-loop-spaces.md)   |
| Groups of loops in 1-types   | [`synthetic-homotopy-theory.groups-of-loops-in-1-types`](synthetic-homotopy-theory.groups-of-loops-in-1-types.md) |
| Iterated loop spaces         | [`synthetic-homotopy-theory.iterated-loop-spaces'](synthetic-homotopy-theory.iterated-loop-spaces.md)             |
| Powers of loops              | [`synthetic-homotopy-theory.powers-of-loops`](synthetic-homotopy-theory.powers-of-loops.md)                       |
| Triple loop spaces           | [`synthetic-homotopy-theory.triple-loop-spaces`](synthetic-homotopy-theory.triple-loop-spaces.md)                 |

## Definitions

### The loop space of a pointed type

```agda
module _
  {l : Level} (A : Pointed-Type l)
  where

  type-Ω : UU l
  type-Ω = Id (point-Pointed-Type A) (point-Pointed-Type A)

  refl-Ω : type-Ω
  refl-Ω = refl

  Ω : Pointed-Type l
  Ω = pair type-Ω refl-Ω
```

### The magma of loops on a pointed space

```agda
module _
  {l : Level} (A : Pointed-Type l)
  where

  mul-Ω : type-Ω A → type-Ω A → type-Ω A
  mul-Ω x y = x ∙ y

  Ω-Magma : Magma l
  pr1 Ω-Magma = type-Ω A
  pr2 Ω-Magma = mul-Ω
```

### The H-space of loops on a pointed space

```agda
module _
  {l : Level} (A : Pointed-Type l)
  where

  left-unit-law-mul-Ω :
    (x : type-Ω A) → Id (mul-Ω A (refl-Ω A) x) x
  left-unit-law-mul-Ω x = left-unit

  right-unit-law-mul-Ω :
    (x : type-Ω A) → Id (mul-Ω A x (refl-Ω A)) x
  right-unit-law-mul-Ω x = right-unit

  Ω-H-Space : H-Space l
  pr1 Ω-H-Space = Ω A
  pr1 (pr2 Ω-H-Space) = mul-Ω A
  pr1 (pr2 (pr2 Ω-H-Space)) = left-unit-law-mul-Ω
  pr1 (pr2 (pr2 (pr2 Ω-H-Space))) = right-unit-law-mul-Ω
  pr2 (pr2 (pr2 (pr2 Ω-H-Space))) = refl
```

### The wild quasigroup of loops on a pointed space

```agda
module _
  {l : Level} (A : Pointed-Type l)
  where

  inv-Ω : type-Ω A → type-Ω A
  inv-Ω = inv

  left-inverse-law-mul-Ω :
    (x : type-Ω A) → Id (mul-Ω A (inv-Ω x) x) (refl-Ω A)
  left-inverse-law-mul-Ω x = left-inv x

  right-inverse-law-mul-Ω :
    (x : type-Ω A) → Id (mul-Ω A x (inv-Ω x)) (refl-Ω A)
  right-inverse-law-mul-Ω x = right-inv x

  Ω-Wild-Quasigroup : Wild-Quasigroup l
  pr1 Ω-Wild-Quasigroup = Ω-Magma A
  pr2 Ω-Wild-Quasigroup = is-binary-equiv-concat
```

### Associativity of concatenation on loop spaces

```agda
module _
  {l : Level} (A : Pointed-Type l)
  where

  associative-mul-Ω :
    (x y z : type-Ω A) →
    Id (mul-Ω A (mul-Ω A x y) z) (mul-Ω A x (mul-Ω A y z))
  associative-mul-Ω x y z = assoc x y z
```

We compute transport of `type-Ω`.

```agda
module _
  {l1 : Level} {A : UU l1} {x y : A}
  where

  equiv-tr-Ω : Id x y → Ω (pair A x) ≃∗ Ω (pair A y)
  equiv-tr-Ω refl = pair id-equiv refl

  equiv-tr-type-Ω : Id x y → type-Ω (pair A x) ≃ type-Ω (pair A y)
  equiv-tr-type-Ω p =
    equiv-pointed-equiv (equiv-tr-Ω p)

  tr-type-Ω : Id x y → type-Ω (pair A x) → type-Ω (pair A y)
  tr-type-Ω p = map-equiv (equiv-tr-type-Ω p)

  is-equiv-tr-type-Ω : (p : Id x y) → is-equiv (tr-type-Ω p)
  is-equiv-tr-type-Ω p = is-equiv-map-equiv (equiv-tr-type-Ω p)

  preserves-refl-tr-Ω : (p : Id x y) → Id (tr-type-Ω p refl) refl
  preserves-refl-tr-Ω refl = refl

  preserves-mul-tr-Ω :
    (p : Id x y) (u v : type-Ω (pair A x)) →
    Id
      ( tr-type-Ω p (mul-Ω (pair A x) u v))
      ( mul-Ω (pair A y) (tr-type-Ω p u) (tr-type-Ω p v))
  preserves-mul-tr-Ω refl u v = refl

  preserves-inv-tr-Ω :
    (p : Id x y) (u : type-Ω (pair A x)) →
    Id
      ( tr-type-Ω p (inv-Ω (pair A x) u))
      ( inv-Ω (pair A y) (tr-type-Ω p u))
  preserves-inv-tr-Ω refl u = refl

  eq-tr-type-Ω :
    (p : Id x y) (q : type-Ω (pair A x)) →
    Id (tr-type-Ω p q) (inv p ∙ (q ∙ p))
  eq-tr-type-Ω refl q = inv right-unit
```

## Properties

### Every pointed identity type is equivalent to a loop space

```agda
module _
  {l : Level} (A : Pointed-Type l) {x : type-Pointed-Type A}
  (p : point-Pointed-Type A ＝ x)
  where

  pointed-equiv-loop-pointed-identity :
    ( pair (point-Pointed-Type A ＝ x) p) ≃∗ Ω A
  pr1 pointed-equiv-loop-pointed-identity =
    equiv-concat' (point-Pointed-Type A) (inv p)
  pr2 pointed-equiv-loop-pointed-identity =
    right-inv p
```
