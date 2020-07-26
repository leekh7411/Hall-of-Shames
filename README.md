# ***Hall of Shames***
Failed list :sob::sob::sob:

## (SQL) INNER JOIN + GROUP BY + MIN,MAX + other column + Two tables
Initialize a table for testing..
```sql
-- A. Create database and table
CREATE TABLE LOL_PRO (
    ID INT PRIMARY KEY AUTO_INCREMENT NOT NULL,
    LEAGUE VARCHAR(32) NOT NULL,
    TEAM VARCHAR(32) NOT NULL,
    RANK INT NOT NULL
);
```

```sql
-- B. Initialize table data
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LPL", "Top Esport", 1);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LPL", "JD Gaming", 2);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LPL", "Invictus Gaming", 3);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LPL", "Suning", 4);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LPL", "Victory Five", 5);

INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LCK", "DRX", 1);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LCK", "DAMWON Gaming", 2);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LCK", "Gen.G", 3);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LCK", "T1", 4);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LCK", "Afreeca Freecs", 5);

INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LEC", "MAD Lions", 1);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LEC", "Rouge", 2);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LEC", "Fnatic", 3);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LEC", "SK Gaming", 4);
INSERT INTO LOL_PRO(LEAGUE, TEAM, RANK) VALUES ("LEC", "Excel Exports", 5);
```
```
+----+--------+-----------------+------+
| ID | LEAGUE | TEAM            | RANK |
+----+--------+-----------------+------+
|  1 | LPL    | Top Esport      |    1 |
|  2 | LPL    | JD Gaming       |    2 |
|  3 | LPL    | Invictus Gaming |    3 |
|  4 | LPL    | Suning          |    4 |
|  5 | LPL    | Victory Five    |    5 |
|  6 | LCK    | DRX             |    1 |
|  7 | LCK    | DAMWON Gaming   |    2 |
|  8 | LCK    | Gen.G           |    3 |
|  9 | LCK    | T1              |    4 |
| 10 | LCK    | Afreeca Freecs  |    5 |
| 11 | LEC    | MAD Lions       |    1 |
| 12 | LEC    | Rouge           |    2 |
| 13 | LEC    | Fnatic          |    3 |
| 14 | LEC    | SK Gaming       |    4 |
| 15 | LEC    | Excel Exports   |    5 |
+----+--------+-----------------+------+
```

```sql
-- C. Best Rank of each League
SELECT LEAGUE, MIN(RANK) AS BEST
FROM LOL_PRO
GROUP BY LEAGUE;
```

```
+--------+--------+
| LEAGUE | RANK_1 |
+--------+--------+
| LCK    |      1 |
| LEC    |      1 |
| LPL    |      1 |
+--------+--------+
```

```sql
-- D. Worst Rank of each League
SELECT LEAGUE, MAX(RANK) AS WORST
FROM LOL_PRO 
GROUP BY LEAGUE;
```

```
+--------+-------+
| LEAGUE | WORST |
+--------+-------+
| LCK    |     5 |
| LEC    |     5 |
| LPL    |     5 |
+--------+-------+
```

```sql
-- E. Best Rank Team of each League
SELECT L.LEAGUE, L.TEAM 
FROM LOL_PRO AS L
INNER JOIN(
    SELECT LEAGUE, MIN(RANK) AS BEST_RANK
    FROM LOL_PRO
    GROUP BY LEAGUE
) AS L_BEST
ON L.LEAGUE = L_BEST.LEAGUE
WHERE L.RANK = L_BEST.BEST_RANK;
```

```
+--------+------------+
| LEAGUE | TEAM       |
+--------+------------+
| LPL    | Top Esport |
| LCK    | DRX        |
| LEC    | MAD Lions  |
+--------+------------+
```

```sql
-- F. Worst Rank Team of each League
SELECT L.LEAGUE, L.TEAM
FROM LOL_PRO AS L
INNER JOIN(
    SELECT LEAGUE, MAX(RANK) AS WORST_RANK
    FROM LOL_PRO
    GROUP BY LEAGUE
) AS L_WORST
ON L.LEAGUE = L_WORST.LEAGUE
WHERE L.RANK = L_WORST.WORST_RANK;
```
```
+--------+----------------+
| LEAGUE | TEAM           |
+--------+----------------+
| LPL    | Victory Five   |
| LCK    | Afreeca Freecs |
| LEC    | Excel Exports  |
+--------+----------------+
```

```sql
-- G. Join two tables
SELECT BRT.LEAGUE, BRT.TEAM AS "Top ranking", WRT.TEAM AS "Bottom ranking"
FROM (
    SELECT L.LEAGUE, L.TEAM 
    FROM LOL_PRO AS L
    INNER JOIN(
        SELECT LEAGUE, MIN(RANK) AS BEST_RANK
        FROM LOL_PRO
        GROUP BY LEAGUE
    ) AS L_BEST
    ON L.LEAGUE = L_BEST.LEAGUE
    WHERE L.RANK = L_BEST.BEST_RANK
) AS BRT
LEFT JOIN(
    SELECT L.LEAGUE, L.TEAM
    FROM LOL_PRO AS L
    INNER JOIN(
        SELECT LEAGUE, MAX(RANK) AS WORST_RANK
        FROM LOL_PRO
        GROUP BY LEAGUE
    ) AS L_WORST
    ON L.LEAGUE = L_WORST.LEAGUE
    WHERE L.RANK = L_WORST.WORST_RANK
) AS WRT
ON BRT.LEAGUE = WRT.LEAGUE
ORDER BY BRT.LEAGUE;
```
```
+--------+------------+----------------+
| LEAGUE | TEAM       | TEAM           |
+--------+------------+----------------+
| LCK    | DRX        | Afreeca Freecs |
| LEC    | MAD Lions  | Excel Exports  |
| LPL    | Top Esport | Victory Five   |
+--------+------------+----------------+
```


## Number matching problem
Described about the problem in the comments of this code
```python
import random
from collections import defaultdict

"""
Number matching problem

- All integers between 1 to R
- The numbers chosen should never be duplicated! (R >= N)
- Target numbers T (size N) -> [t1,t2,t3,...t_n-1, t_n]
- Chosen numbers C (size N-M) [c1,c2,...,c_n-m-1, c_n-m] with empty numbers (size M) [e1,e2,...,e_m-1,e_m]
- N > M

- Q. What is the maximum matching count for a given selected numbers?
- Q. What is the minimum matching count for a given selected numbers?

"""
def load_random_inputs(R=100,N=10,M=5):
    assert R > N and N > M
    numbers = list(range(1,R+1))
    random.shuffle(numbers)
    target_numbers = numbers[:N]
    random.shuffle(numbers)
    chosen_numbers = numbers[:N-M] + [0] * M
    return target_numbers, chosen_numbers

"""
Just count matchings without empty numbers
"""
def search_minimum_matching(target_numbers, chosen_numbers):
    matching_count = 0
    for c in chosen_numbers:
        if c in target_numbers:
            matching_count += 1
    return matching_count

"""
Maximum matching is a simple counting with empty numbers using target numbers not chosen 
"""
def search_maximum_matching(target_numbers, chosen_numbers, M):
    matching_count = 0
    possible_numbers = []
    for t in target_numbers:
        if t not in chosen_numbers:
            possible_numbers.append(t)
    
    min_count = search_minimum_matching(target_numbers, chosen_numbers)
    max_count = min_count + min(len(possible_numbers), M)
    return max_count
```

Let's run this code...

```python
"""
>> R: 100 / all possible numbers are between 1 to 100
>> N: 10  / we will choose 10 numbers in R without duplicate
>> M: 5   / 5 numbers (N-M) are fixed and others (M) are empty 
"""
R, N, M = 100, 10, 5
T, C    = load_random_inputs(R,N,M)

minimum_matchings = search_minimum_matching(T,C)
maximum_matchings = search_maximum_matching(T,C,M)
print("> Target numbers : {}".format(T))
print("> Chosen numbers : {}".format(C))
print("> Minimum matchs : {}".format(minimum_matchings))
print("> Maximum matchs : {}".format(maximum_matchings))
```


Output is ...

```python
> Target numbers : [70, 69, 5, 49, 59, 68, 74, 100, 89, 35]
> Chosen numbers : [51, 4, 97, 18, 55, 0, 0, 0, 0, 0]
> Minimum matchs : 0
> Maximum matchs : 5
```


## Any single list about *N* dices with sum *S*
```python
def __any_single_list_about_N_dices_with_sum_S(N,S):
    N = N       # number of possible dices
    D = 6-1     # maximum of dice
    S = S       # sum of target
    R = S - N   # at least 1 x 10 values are fixed
    if R <= 0    : return [], 0, "Failed"
    if R > (N*D) : return [], 0, "Failed"
    cur_sum = 0 
    dices = []
    initial_dices = [1] * N
    for i in range(N):
        if cur_sum + D < R:
            cur_sum += D
            dices.append(D)
        elif cur_sum + D == R:
            dices.append(D)
            break
        else: # cur_sum + D > R
            remain_sum = R - cur_sum
            if remain_sum > D: 
                # impossible case!
                dices.append(-1)
                return [], 0, "Failed"
            else: 
                # remain_sum < D
                # D <- remain_sum
                dices.append(remain_sum)
                break

    dices += [0] * (N - len(dices))
    final_dices = [i+d for i,d in zip(initial_dices, dices)]
    return final_dices, sum(final_dices), "Success"
``` 
 Run this method...
 
```python
print(__any_single_list_about_N_dices_with_sum_S(N=10, S=59))
```
Output
```python
([6, 6, 6, 6, 6, 6, 6, 6, 6, 5], 59, 'Success')
```

## Very simple recursive permutation with sum *S*
```python
def permutations_with_sum_M(M, logging=False):
    possibles = []
    def multi_loop(K, vals):
        if K < 1:
            possibles.append((vals, sum(vals)))
            return

        for i in range(1,K+1):
            multi_loop(K-i, vals + [i])

    multi_loop(K=M, vals=[])
    print("- number of permutations with sum M({}) : {}".format(M,len(possibles)))
    if logging: 
        print(possibles)
``` 

Run this method..
 
```python
permutations_with_sum_M(M=0, logging=True)
permutations_with_sum_M(M=6, logging=True)
permutations_with_sum_M(M=8, logging=True)
``` 

Output

```python
- number of permutations with sum M(0) : 1
[([], 0)]
- number of permutations with sum M(6) : 32
[([1, 1, 1, 1, 1, 1], 6), ([1, 1, 1, 1, 2], 6), ([1, 1, 1, 2, 1], 6), ([1, 1, 1, 3], 6), ([1, 1, 2, 1, 1], 6), ([1, 1, 2, 2], 6), ([1, 1, 3, 1], 6), ([1, 1, 4], 6), ([1, 2, 1, 1, 1], 6), ([1, 2, 1, 2], 6), ([1, 2, 2, 1], 6), ([1, 2, 3], 6), ([1, 3, 1, 1], 6), ([1, 3, 2], 6), ([1, 4, 1], 6), ([1, 5], 6), ([2, 1, 1, 1, 1], 6), ([2, 1, 1, 2], 6), ([2, 1, 2, 1], 6), ([2, 1, 3], 6), ([2, 2, 1, 1], 6), ([2, 2, 2], 6), ([2, 3, 1], 6), ([2, 4], 6), ([3, 1, 1, 1], 6), ([3, 1, 2], 6), ([3, 2, 1], 6), ([3, 3], 6), ([4, 1, 1], 6), ([4, 2], 6), ([5, 1], 6), ([6], 6)]
- number of permutations with sum M(8) : 128
[([1, 1, 1, 1, 1, 1, 1, 1], 8), ([1, 1, 1, 1, 1, 1, 2], 8), ([1, 1, 1, 1, 1, 2, 1], 8), ([1, 1, 1, 1, 1, 3], 8), ([1, 1, 1, 1, 2, 1, 1], 8), ([1, 1, 1, 1, 2, 2], 8), ([1, 1, 1, 1, 3, 1], 8), ([1, 1, 1, 1, 4], 8), ([1, 1, 1, 2, 1, 1, 1], 8), ([1, 1, 1, 2, 1, 2], 8), ([1, 1, 1, 2, 2, 1], 8), ([1, 1, 1, 2, 3], 8), ([1, 1, 1, 3, 1, 1], 8), ([1, 1, 1, 3, 2], 8), ([1, 1, 1, 4, 1], 8), ([1, 1, 1, 5], 8), ([1, 1, 2, 1, 1, 1, 1], 8), ([1, 1, 2, 1, 1, 2], 8), ([1, 1, 2, 1, 2, 1], 8), ([1, 1, 2, 1, 3], 8), ([1, 1, 2, 2, 1, 1], 8), ([1, 1, 2, 2, 2], 8), ([1, 1, 2, 3, 1], 8), ([1, 1, 2, 4], 8), ([1, 1, 3, 1, 1, 1], 8), ([1, 1, 3, 1, 2], 8), ([1, 1, 3, 2, 1], 8), ([1, 1, 3, 3], 8), ([1, 1, 4, 1, 1], 8), ([1, 1, 4, 2], 8), ([1, 1, 5, 1], 8), ([1, 1, 6], 8), ([1, 2, 1, 1, 1, 1, 1], 8), ([1, 2, 1, 1, 1, 2], 8), ([1, 2, 1, 1, 2, 1], 8), ([1, 2, 1, 1, 3], 8), ([1, 2, 1, 2, 1, 1], 8), ([1, 2, 1, 2, 2], 8), ([1, 2, 1, 3, 1], 8), ([1, 2, 1, 4], 8), ([1, 2, 2, 1, 1, 1], 8), ([1, 2, 2, 1, 2], 8), ([1, 2, 2, 2, 1], 8), ([1, 2, 2, 3], 8), ([1, 2, 3, 1, 1], 8), ([1, 2, 3, 2], 8), ([1, 2, 4, 1], 8), ([1, 2, 5], 8), ([1, 3, 1, 1, 1, 1], 8), ([1, 3, 1, 1, 2], 8), ([1, 3, 1, 2, 1], 8), ([1, 3, 1, 3], 8), ([1, 3, 2, 1, 1], 8), ([1, 3, 2, 2], 8), ([1, 3, 3, 1], 8), ([1, 3, 4], 8), ([1, 4, 1, 1, 1], 8), ([1, 4, 1, 2], 8), ([1, 4, 2, 1], 8), ([1, 4, 3], 8), ([1, 5, 1, 1], 8), ([1, 5, 2], 8), ([1, 6, 1], 8), ([1, 7], 8), ([2, 1, 1, 1, 1, 1, 1], 8), ([2, 1, 1, 1, 1, 2], 8), ([2, 1, 1, 1, 2, 1], 8), ([2, 1, 1, 1, 3], 8), ([2, 1, 1, 2, 1, 1], 8), ([2, 1, 1, 2, 2], 8), ([2, 1, 1, 3, 1], 8), ([2, 1, 1, 4], 8), ([2, 1, 2, 1, 1, 1], 8), ([2, 1, 2, 1, 2], 8), ([2, 1, 2, 2, 1], 8), ([2, 1, 2, 3], 8), ([2, 1, 3, 1, 1], 8), ([2, 1, 3, 2], 8), ([2, 1, 4, 1], 8), ([2, 1, 5], 8), ([2, 2, 1, 1, 1, 1], 8), ([2, 2, 1, 1, 2], 8), ([2, 2, 1, 2, 1], 8), ([2, 2, 1, 3], 8), ([2, 2, 2, 1, 1], 8), ([2, 2, 2, 2], 8), ([2, 2, 3, 1], 8), ([2, 2, 4], 8), ([2, 3, 1, 1, 1], 8), ([2, 3, 1, 2], 8), ([2, 3, 2, 1], 8), ([2, 3, 3], 8), ([2, 4, 1, 1], 8), ([2, 4, 2], 8), ([2, 5, 1], 8), ([2, 6], 8), ([3, 1, 1, 1, 1, 1], 8), ([3, 1, 1, 1, 2], 8), ([3, 1, 1, 2, 1], 8), ([3, 1, 1, 3], 8), ([3, 1, 2, 1, 1], 8), ([3, 1, 2, 2], 8), ([3, 1, 3, 1], 8), ([3, 1, 4], 8), ([3, 2, 1, 1, 1], 8), ([3, 2, 1, 2], 8), ([3, 2, 2, 1], 8), ([3, 2, 3], 8), ([3, 3, 1, 1], 8), ([3, 3, 2], 8), ([3, 4, 1], 8), ([3, 5], 8), ([4, 1, 1, 1, 1], 8), ([4, 1, 1, 2], 8), ([4, 1, 2, 1], 8), ([4, 1, 3], 8), ([4, 2, 1, 1], 8), ([4, 2, 2], 8), ([4, 3, 1], 8), ([4, 4], 8), ([5, 1, 1, 1], 8), ([5, 1, 2], 8), ([5, 2, 1], 8), ([5, 3], 8), ([6, 1, 1], 8), ([6, 2], 8), ([7, 1], 8), ([8], 8)]
``` 

## Very simple recursive permutation about *N* items with sum *M*
```python
def permutations_N_coins_with_sum_M(N,M, logging=False):
    possibles = []
    depth_limit = N
    def multi_loop(K, vals, depth):
        if K < 1:
            if depth == depth_limit:
                possibles.append((vals, sum(vals)))
            return
        if depth == depth_limit:
            return
        
        for i in range(1,K+1):
            multi_loop(K-i, vals + [i], depth+1)

    multi_loop(K=M, vals=[], depth=0)
    print("- number of permutations about N({}) items with sum M({}) : {}".format(N,M,len(possibles)))
    if logging: 
        print(possibles)
``` 

Let's run this code...

```python
permutations_N_coins_with_sum_M(N=6, M=0)
permutations_N_coins_with_sum_M(N=6, M=6, logging=True)
permutations_N_coins_with_sum_M(N=6, M=10, logging=True)
permutations_N_coins_with_sum_M(N=6, M=20, logging=False)
```

Output is ...

```python
- number of permutations about N(6) items with sum M(0) : 0
- number of permutations about N(6) items with sum M(6) : 1
[([1, 1, 1, 1, 1, 1], 6)]
- number of permutations about N(6) items with sum M(10) : 126
[([1, 1, 1, 1, 1, 5], 10), ([1, 1, 1, 1, 2, 4], 10), ([1, 1, 1, 1, 3, 3], 10), ([1, 1, 1, 1, 4, 2], 10), ([1, 1, 1, 1, 5, 1], 10), ([1, 1, 1, 2, 1, 4], 10), ([1, 1, 1, 2, 2, 3], 10), ([1, 1, 1, 2, 3, 2], 10), ([1, 1, 1, 2, 4, 1], 10), ([1, 1, 1, 3, 1, 3], 10), ([1, 1, 1, 3, 2, 2], 10), ([1, 1, 1, 3, 3, 1], 10), ([1, 1, 1, 4, 1, 2], 10), ([1, 1, 1, 4, 2, 1], 10), ([1, 1, 1, 5, 1, 1], 10), ([1, 1, 2, 1, 1, 4], 10), ([1, 1, 2, 1, 2, 3], 10), ([1, 1, 2, 1, 3, 2], 10), ([1, 1, 2, 1, 4, 1], 10), ([1, 1, 2, 2, 1, 3], 10), ([1, 1, 2, 2, 2, 2], 10), ([1, 1, 2, 2, 3, 1], 10), ([1, 1, 2, 3, 1, 2], 10), ([1, 1, 2, 3, 2, 1], 10), ([1, 1, 2, 4, 1, 1], 10), ([1, 1, 3, 1, 1, 3], 10), ([1, 1, 3, 1, 2, 2], 10), ([1, 1, 3, 1, 3, 1], 10), ([1, 1, 3, 2, 1, 2], 10), ([1, 1, 3, 2, 2, 1], 10), ([1, 1, 3, 3, 1, 1], 10), ([1, 1, 4, 1, 1, 2], 10), ([1, 1, 4, 1, 2, 1], 10), ([1, 1, 4, 2, 1, 1], 10), ([1, 1, 5, 1, 1, 1], 10), ([1, 2, 1, 1, 1, 4], 10), ([1, 2, 1, 1, 2, 3], 10), ([1, 2, 1, 1, 3, 2], 10), ([1, 2, 1, 1, 4, 1], 10), ([1, 2, 1, 2, 1, 3], 10), ([1, 2, 1, 2, 2, 2], 10), ([1, 2, 1, 2, 3, 1], 10), ([1, 2, 1, 3, 1, 2], 10), ([1, 2, 1, 3, 2, 1], 10), ([1, 2, 1, 4, 1, 1], 10), ([1, 2, 2, 1, 1, 3], 10), ([1, 2, 2, 1, 2, 2], 10), ([1, 2, 2, 1, 3, 1], 10), ([1, 2, 2, 2, 1, 2], 10), ([1, 2, 2, 2, 2, 1], 10), ([1, 2, 2, 3, 1, 1], 10), ([1, 2, 3, 1, 1, 2], 10), ([1, 2, 3, 1, 2, 1], 10), ([1, 2, 3, 2, 1, 1], 10), ([1, 2, 4, 1, 1, 1], 10), ([1, 3, 1, 1, 1, 3], 10), ([1, 3, 1, 1, 2, 2], 10), ([1, 3, 1, 1, 3, 1], 10), ([1, 3, 1, 2, 1, 2], 10), ([1, 3, 1, 2, 2, 1], 10), ([1, 3, 1, 3, 1, 1], 10), ([1, 3, 2, 1, 1, 2], 10), ([1, 3, 2, 1, 2, 1], 10), ([1, 3, 2, 2, 1, 1], 10), ([1, 3, 3, 1, 1, 1], 10), ([1, 4, 1, 1, 1, 2], 10), ([1, 4, 1, 1, 2, 1], 10), ([1, 4, 1, 2, 1, 1], 10), ([1, 4, 2, 1, 1, 1], 10), ([1, 5, 1, 1, 1, 1], 10), ([2, 1, 1, 1, 1, 4], 10), ([2, 1, 1, 1, 2, 3], 10), ([2, 1, 1, 1, 3, 2], 10), ([2, 1, 1, 1, 4, 1], 10), ([2, 1, 1, 2, 1, 3], 10), ([2, 1, 1, 2, 2, 2], 10), ([2, 1, 1, 2, 3, 1], 10), ([2, 1, 1, 3, 1, 2], 10), ([2, 1, 1, 3, 2, 1], 10), ([2, 1, 1, 4, 1, 1], 10), ([2, 1, 2, 1, 1, 3], 10), ([2, 1, 2, 1, 2, 2], 10), ([2, 1, 2, 1, 3, 1], 10), ([2, 1, 2, 2, 1, 2], 10), ([2, 1, 2, 2, 2, 1], 10), ([2, 1, 2, 3, 1, 1], 10), ([2, 1, 3, 1, 1, 2], 10), ([2, 1, 3, 1, 2, 1], 10), ([2, 1, 3, 2, 1, 1], 10), ([2, 1, 4, 1, 1, 1], 10), ([2, 2, 1, 1, 1, 3], 10), ([2, 2, 1, 1, 2, 2], 10), ([2, 2, 1, 1, 3, 1], 10), ([2, 2, 1, 2, 1, 2], 10), ([2, 2, 1, 2, 2, 1], 10), ([2, 2, 1, 3, 1, 1], 10), ([2, 2, 2, 1, 1, 2], 10), ([2, 2, 2, 1, 2, 1], 10), ([2, 2, 2, 2, 1, 1], 10), ([2, 2, 3, 1, 1, 1], 10), ([2, 3, 1, 1, 1, 2], 10), ([2, 3, 1, 1, 2, 1], 10), ([2, 3, 1, 2, 1, 1], 10), ([2, 3, 2, 1, 1, 1], 10), ([2, 4, 1, 1, 1, 1], 10), ([3, 1, 1, 1, 1, 3], 10), ([3, 1, 1, 1, 2, 2], 10), ([3, 1, 1, 1, 3, 1], 10), ([3, 1, 1, 2, 1, 2], 10), ([3, 1, 1, 2, 2, 1], 10), ([3, 1, 1, 3, 1, 1], 10), ([3, 1, 2, 1, 1, 2], 10), ([3, 1, 2, 1, 2, 1], 10), ([3, 1, 2, 2, 1, 1], 10), ([3, 1, 3, 1, 1, 1], 10), ([3, 2, 1, 1, 1, 2], 10), ([3, 2, 1, 1, 2, 1], 10), ([3, 2, 1, 2, 1, 1], 10), ([3, 2, 2, 1, 1, 1], 10), ([3, 3, 1, 1, 1, 1], 10), ([4, 1, 1, 1, 1, 2], 10), ([4, 1, 1, 1, 2, 1], 10), ([4, 1, 1, 2, 1, 1], 10), ([4, 1, 2, 1, 1, 1], 10), ([4, 2, 1, 1, 1, 1], 10), ([5, 1, 1, 1, 1, 1], 10)]
- number of permutations about N(6) items with sum M(20) : 11628
```

