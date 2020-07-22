# **Hall-of-Shames**
Failed list

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
