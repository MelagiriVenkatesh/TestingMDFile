# Efficiently Compute a^(x) in log(N) Time with O(1) space

Created By: Venkatesh
Created: December 4, 2024 2:46 PM
Last Edited Time: December 4, 2024 5:30 PM
Last Edited By: Venkatesh

- we know that any number can be represented in summation of 2’s powers i.e..,

```cpp
 5   =   (4 + 1)          =  (2^2 + 2^0)               =  5    
 9   =   (8 + 1)          =  (2^3 + 2^0)               =  9  
15   =   (8 + 4 + 2 + 1)  =  (2^3 + 2^2 + 2^1 + 2^0)   =  15

And so on for any number we can write it in terms of (summation of 2's powers)
```

### How to find a^(x) and how is the above point helping us to find a^(x)?

- Now let’s say you want to find a^(x) i.e.., for example 5 ^ (13) Ok.
- Now what we can do is represent exponent 13 in terms of summation of 2’s powers i.e..,
    
    ```cpp
         13 = (8 + 4 + 1) Now raise both sides with the given base i.e.., 
    															here in this case base = 5. we get
    		
    		 5^(13) = 5^(8 + 4 + 1)        i.e.., 
    		 5^(13) = 5^8 * 5^4 * 5^1      [by a^m * a^n = a^(m+n) property]
    ```
    
- Now we iterate a loop where we check LSB ( least significant bit ) of Exponent and multiply that LSB with the current base value.
- And after that we take max(1, LSB * current_base_value) and multiply it with res and then store it in res itself. Then we left shift (>>) exponent by 1 to get the next LSB of the exponent and this loop continues until exponent > 0.
- Now the question is why do we need max(1, LSB * current_base_value) Operation?
    - Before knowing the operation first we will know that in every operation we will multiply base with itself and store that answer in base itself only. But But Butt why?
    - imagine we have a bits of a number i.e.., 0th bit, 1st bit, 2nd bit, 3rd bit and so on. Like
        
        ![image.png](Efficiently%20Compute%20a%5E(x)%20in%20log(N)%20Time%20with%20O(1)%2015226c193e1580f6aaeaf087a89f8f42/image.png)
        
    - Here we can say that as we move towards left, values in cells gets multiplied by 2 or the next value is nothing but addition of previous value with itself i.e..,
        - next value = (prev value + prev value)  [8 = 4 + 4]
        - next value = (prev value * 2)                 [8 = 4  * 2]
    
    - Now if you look at the exponent i.e.., 13 = 1101 in binary representation.
    - so now we have cells values which represents the powers or exponents of base i.e..,
    - `13 (1101) ⇒ 8 4 2 1 = 8*1 + 4*1 + 2*0 + 1*1 = 13`. Now let see a piece of code to understand the operation or the main logic
        
        ```cpp
        int binary_exponentiation(int base, int exponent) {
        
        			int res = 1;
        			while(exponent > 0) {
        					
        					int LSB = (exponent & 1);  
        					res = res * max(1, LSB * base);
        					exponent >>= 1;
        					base *= base;
        			}
        			return res;
        }
        
        // LSB = Least Signifient Bit [0/1]
        // (base = base * base) <=> (base = pow(base,2))
        ```
        
    - Here in above code we can see that the base is getting multiplied by itself in every iteration i.e.., if base = 5 then in
        - Iteration 0:    base = 5. (Not entered loop yet)
        - Iteration 1:    base *= base; i.e.., base becomes 5^2
        - Iteration 2:    base *= base; i.e.., base becomes 5^4
        - Iteration 3:    base *= base; i.e.., base becomes 5^8    and so on.
    
    - But you might think why are we doing base = base * base it is because we want 5^2 in next iteration and 5^4 in next next iteration and 5^8 in next next next iteration. And the way I got to this conclusion that base = base * base is by doing the following
        - to make 5^2 equal to 5^4 I need to add 2 to the 5^2‘s exponent right i.e.., 
            5^(2+2) will get equal to 5^4 right similarly 5^4 will get equal to 5^8 by adding 4 to 5^4’s exponent i.e.., 5^(4+4) will be now equal to 5^8 right.
        - We know that `a^m * a^n = a^(m+n)`  and based on this property we can say that 
           5^(2+2) = 5^2 * 5^2.
    - Therefore we get the next term (i.e.., base in the next iteration) by multiplying the base in current iteration with itself and store that value in base only ok.
    - we are doing res = res * max(1, LSB * base); because to get value of 5^13 in pieces like below
    - **`5^(13) = (5^8) * (5^4) * (5^1) = 5^(8+4+1) = 5^(13)`**  *or more formally like below*
    - **`5^(13) = max(1, 1*(5^8)) * max(1, 1*(5^4)) * max(1, 0*(5^2)) * max(1, 1*(5^1))`**    *here in the above equation we are multiple the piece of the left side with 0 or 1 and that is 0 or 1 is nothing but LSB of exponent in that iteration i.e.., 
    13 = (1101) in binary system. so that’s why 1*5^8, 1*5^4, 0*5^2, 1*5^1 respectively.*
    - Again we are taking max(1, 1*(5^4)) and max(1, 0*(5^2)) because when the bit value or LSB where the exponent 13 is 0 there we are making sure that (LSB * base) does not goes 0. if it does then it will be multiplied with res and stored in res itself which will negate all the computation because 0 multiplied with anything is 0. so that’s why using 
    max(1, LSB * base)) instead of  (LSB * base) to avoid 0 getting multiplied with res in this below line
                                      `res = res * max(1, (LSB * base));`    (right)
                                      `res = res * (LSB * base);`                   (wrong)

- So at the end we will have **“res”** as below
  `res = 5^8 * 5^4 * 5^1 = 5^(8+4+1) = 5^13`  which is what exactly we needed to compute.
