# Common Tactics

```lean4
intro
revert
- opposite of intro
rename_i
induction
- induction <n> generalizing m
- induction <n> using <func>.induct
    - Note that <func>.induct has the advantage of being able to properly prove exclusitivityy of cases, which is not always possible with regular match. <func>.induct can be replaced by e.g. Nat.le_induction. 
    - with induction (or cases on inductive type sometimes), you cannot do it on a non variable so in some cases you must do generalize
case
- case h1 | h2 for multiple
rw
simp
- simp_arith
- simp[] at h
- simp_all is more powerful than simp[*] at *
- simp[-x] to exclude x
- simp only [h]
omega
- use local context about inequalities/contstraints to help out linarith (useful for weird Nat subtraction scenarios)
linarith
calc
try
all_goals
<;>
repeat
first | t1 | t2 | t3
- try t1 else t2 else t3
apply
exact
decide
- if goal is decidable
refine
cases
- cases [hp :] expr
split
- useful for match and if statements
- you can even do `split at <h>` when h is some complex match statement
contradiction
suffices
try
let
- note that let definitions may be unfolded by the compiler, but have cant
have
trivial 
generalize
- generalize [h :=] expr = var replaces expression with a variable, useful for some induction scenarios
unfold
- but generally simp does the trick
exists
- short hand for apply exists.intro x
constructor
- applies first matching constructor
left
- force first constructor
right
- force second constructor
solve_by_elim
- recursively keep applying constructors
sorry
- op!? 
funext
- show two functions are equal by showing equal inputs map to equal values
```

classical
```
by_cases (prop)
```

language features
```
inductive
deriving
```

## non tactics 

```lean4
noConfusion -- constructor is injective
induct
```

## notes

### More Complex Inductive Types
Recall that to proving a proposition is equivalent to creating an instance of that type.
Consequently, we may eveen have inductive types that take arguments.
```lean4
inductive palindrome : List Nat -> Prop where
 -- it's incorrect to say emp : palindrome since palindrome itself isn't a type
 -- we must specify the argument to actually receive a type
  | emp : palindrome []
  | sng : ∀ (n : Nat), palindrome [n]
  | cns : ∀ (n : Nat) (ns : List Nat), palindrome ns -> palindrome (n :: ns ++ [n])

-- we can then use induction on p (or cases if that's all we need)

```

### Equation Order
typically simp converts lhs to rhs, so make the lhs the more complex
and the rhs and the easier to work with. In particular, note that 
changing the direction of equality changes whether or not simp works in the following proof.
```lean4
def sum (n : Nat) : Nat :=
    match n with
    | 0 => 0
    | n' + 1 => n + sum n'

def tail_rec_sum (n res : Nat) : Nat :=
    match n with
    | 0 => res
    | n' + 1 => tail_rec_sum n' (res + n)

def tr_sum (n : Nat) : Nat := tail_rec_sum n 0

theorem tail_rec_sum_add : ∀ (n res: ℕ), tail_rec_sum n res = (sum n) + res := by
    intro n
    induction n
    case zero =>
        intro res
        simp[sum, tail_rec_sum]
    case succ n' ih =>
        intro res
        simp_arith[tail_rec_sum, sum, ih]
        -- rw [←ih (res + (n' + 1))]
```
