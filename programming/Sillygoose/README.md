# Sillygoose
@ChallengeAuthor: Connor Chang
@WriteupAuthor: [MrSharkSpamBot]\

## Description
There's no way you can guess my favorite number, you silly goose. 

## Understanding the code
```
from random import randint
import time
ans = randint(0, pow(10, 100))
start_time = int(time.time())
turns = 0
while True:
    turns += 1

    inp = input()

    if int(time.time()) > start_time + 60:
       print("you ran out of time you silly goose") 
       break

    if "q" in inp:
        print("you are no fun you silly goose")
        break

    if not inp.isdigit():
        print("give me a number you silly goose")
        continue

    inp = int(inp)
    if inp > ans:
        print("your answer is too large you silly goose")
    elif inp < ans:
        print("your answer is too small you silly goose")
    else:
        print("congratulations you silly goose")
        f = open("/flag.txt", "r")
        print(f.read())

    if turns > 500:
        print("you have a skill issue you silly goose")
```
The following code is provided by the challenge. This is a simple guess the number game with input validation. The only issue is that the range is between 0 and 10 to the power of 10. Luckily enough, we can write an agorithm to solve this challenge based off of the large or small output provided.

## Unintended
```
from pwn import *
import random

best_guess = random.randint(0, pow(10, 100))
smallest = 0
largest = pow(10, 100)

conn = remote('24.199.110.35', 41199)
conn.sendline(str(best_guess).encode())

while True:
    output = conn.recvline().decode()

    if 'small' in output:
        smallest = best_guess + 1
    elif 'large' in output:
        largest = best_guess - 1
    elif 'congratulations' in output:
        print(output)
        output = conn.recvline().decode()
        print(output)
        conn.close()
        break
    else:
        print(output)
        break

    best_guess = random.randint(smallest, largest)
    conn.sendline(str(best_guess).encode())
```
The above code above is my unintended solution. It is rather similar to binary search except more stupid. If you run this code 100 times, you'll probably get the flag once. My dumbass somehow got the flag on the first try with this code. Basically it randomly generates a number between 0 and 10 to the power of ten, then based off of the output of small or large, sets the variables small or large as the lower or upper bound just like in a [binary search] algorithm, but instead of being smart and dividing the best guess number by two, it continues to use the lower and upper bound in to randomly generate the answer. The algorithm I wrote above is similar to what is called a [genetic algorithm] to those who are interested. These algorithms are used a lot in scientific computing and are actually useful unlike the one I wrote above. If you do ever get the solution from this algorithm, it should look like:
```
[x] Opening connection to 24.199.110.35 on port 41199
[x] Opening connection to 24.199.110.35 on port 41199: Trying 24.199.110.35
[+] Opening connection to 24.199.110.35 on port 41199: Done
congratulations you silly goose

n00bz{y0u_4r3_4_sm4rt_51l1y_g0053}

[*] Closed connection to 24.199.110.35 port 41199
```
Though most of the time, you will probaby get:
```
[x] Opening connection to 24.199.110.35 on port 41199
[x] Opening connection to 24.199.110.35 on port 41199: Trying 24.199.110.35
[+] Opening connection to 24.199.110.35 on port 41199: Done
you ran out of time you silly goose

[*] Closed connection to 24.199.110.35 port 41199
```

## Intended
```
from pwn import *

smallest = 0
largest = pow(10, 100)

conn = remote('24.199.110.35', 41199)

while True:
    best_guess = (smallest + largest) // 2
    conn.sendline(str(best_guess).encode())
    output = conn.recvline().decode()

    if 'small' in output:
        smallest = best_guess + 1
    elif 'large' in output:
        largest = best_guess - 1
    elif 'congratulations' in output:
        print(output)
        output = conn.recvline().decode()
        print(output)
        conn.close()
        break
```
The above code is the intended solution which is essentially just a simple [binary search] algorithm. The first guess is 0 plus 10 to the power of 100 divided by two. Then based off of the output of small or large, the algorithm will set a upper or lower bound to restrict the possible numbers to be searched through. Since the algorithm continuously halfs the number of possible solutions till it finds the solution, it is able to solve the challenge in under a minute and less than 500 attempts each time. The output should look like:
```
[x] Opening connection to 24.199.110.35 on port 41199
[x] Opening connection to 24.199.110.35 on port 41199: Trying 24.199.110.35
[+] Opening connection to 24.199.110.35 on port 41199: Done
congratulations you silly goose

n00bz{y0u_4r3_4_sm4rt_51l1y_g0053}

[*] Closed connection to 24.199.110.35 port 41199
```

[MrSharkSpamBot]: https://github.com/MrSharkSpamBot
[genetic algorithm]: https://www.mathworks.com/help/gads/what-is-the-genetic-algorithm.html
[binary search]: https://www.geeksforgeeks.org/binary-search/
