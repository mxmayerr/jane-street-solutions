
## January 2025: Somewhat Square Sudoku
Date: January 2025  
Link: [click here](https://www.janestreet.com/puzzles/somewhat-square-sudoku-index/)  
Solved on: January 31, 2025  

---

### The Puzzle

We are tasked with filling in a 9x9 Sudoku-style grid with digits from 0-9, such that the 9-digit numbers formed by the rows have the highest possible GCD over any such grid.

### Initial Observations/Questions

- The number $2025$ is placed twice in the board for us—could this be a hint to something?
  - Can the placement of $2025$ help determine where other numbers should go?
- $2025$ is a square number ($45^{2}$). Does this relate to the title *"Somewhat Square"*?
- Only nine of the ten 1-digit numbers are included in the board. We already know that $0, 2, 5$ are included, so one of $1,3,4,6,7,8,9$ is *not* included.
- Using Sudoku rules, we can place a $2$ in the middle cell of the top middle box, but we cannot yet place any other numbers.

### Initial Strategy

My initial strategy was to determine which of the ten digits could be removed and where a leading zero might be placed to narrow down possible results. However, this approach did not provide enough information.

Instead, I focused on the sum of the rows/columns, leveraging a special property of Sudoku boards: the sum of digits in each row, column, and box is equal. The key was to frame this as an equation.

### Strategy

> Note: The following strategy may not be the most efficient process to solve the problem, but it is how I went about it in my head.

Let's define each row as $r_n$, where $n$ is the row number from $1$ to $9$. We define the greatest common divisor (GCD), which is currently unknown, as $\gcd$. Given this, we can express each row as:

$$
r_n = \gcd \cdot x_n, \quad \text{where } x_n \text{ is some integer}.
$$

Applying this to all rows:

$$
\begin{aligned}
    r_1 &= \gcd \cdot x_1 \\
    r_2 &= \gcd \cdot x_2 \\
    &\vdots \\
    r_9 &= \gcd \cdot x_9
\end{aligned}
$$

To find the sum of these $9$ equations, we introduce a new variable, $s$, which represents the sum of the nine chosen digits (excluding one). Due to Sudoku rules, $s$ is between $36$ and $45$, but this constraint is not immediately relevant.

$$
\begin{aligned}
    \text{Sum of rows} &= 10^0 \cdot s + 10^1 \cdot s + 10^2 \cdot s + \dots + 10^8 \cdot s \\
    &= s \cdot (10^0 + 10^1 + 10^2 + \dots + 10^8) \\
    &= s \cdot 111,111,111
\end{aligned}
$$

Now we know the sum of *all* the rows given the digits included:

$$
111,111,111 \cdot s = \gcd \cdot (x_1 + x_2 + \dots + x_9)
$$

To better understand $\gcd$, we perform a prime factorization:

$$
111,111,111 = 3^2 \cdot 37 \cdot 333667
$$

Since $\gcd$ must be a multiple of one of these prime factors, we start by testing $333667$ as a potential $\gcd$.

### Code

First, I wrote a function to check if a number has all unique digits (as required by Sudoku rules). Initially, I used a dictionary (hash map), but a simpler approach is to compare the length of the number and its set equivalent:

```python
def all_digits_unique(num):
    return len(str(num)) == len(set(str(num)))
```

Next, I wrote a function to check if a number satisfies the second row of the grid, which has two digits pre-filled:

```python
def satisfy_row_2(num):
    # _ _ _ _ 2 _ _ _ 5
    num_str = str(num)
    return num_str[-5] == "2" and num_str[-1] == "5"
```
_Note: We access the digits from the back to avoid indexing errors in case of a leading zero._

Now, we define:

```python
a0 = 333667
a = a0 * (10_000_000 // a0) # Ensure a is at least 8 digits
```

We then iterate through possible values of $a$, ensuring uniqueness of digits and row constraints:

```python
while a < 999_999_999:
    if all_digits_unique(str(a)) and satisfy_row_2(str(a)):
        print(a)
    a += a0
```
Output:
```bash
61728395
```
Since we get only one result, we place $61728395$ in row 2 of the grid. Notably, the digit $4$ is missing from the grid, so we exclude any numbers containing $4$ in future checks.

For row 1:

```python
def satisfy_row_1(num):
    # _ _ _ _ _ _ _ 2 _
    num_str = str(num)
    return num_str[-2] == "2"

while a < 999_999_999:
    if "4" not in str(a) and all_digits_unique(str(a)) and satisfy_row_1(str(a)):
        print(a)
    a += a0
```
Output:
```bash
193860527
395061728
```

Since $193860527$ has an $8$ in position $3$, and row 2 already has an $8$ in position $4$ (same box, violating Sudoku rules), we choose $395061728$.

Continuing this approach for all rows (omitted here for brevity), we find that the middle row must be $283950617$. Thus, the final grid is:

### Final Grid

$$
\begin{array}{|c|c|c||c|c|c||c|c|c|}
\hline
3 & 9 & 5 & 0 & 6 & 1 & 7 & \mathbf{2} & 8 \\
0 & 6 & 1 & 7 & 2 & 8 & 3 & 9 & \mathbf{5} \\
7 & \mathbf{2} & 8 & 3 & 9 & 5 & 0 & 6 & 1 \\
\hline\hline
9 & 5 & \mathbf{0} & 6 & 1 & 7 & 2 & 8 & 3 \\
\mathit{2} & \mathit{8} & \mathit{3} & \mathit{9} & \mathit{5} & \mathit{0} & \mathit{6} & \mathit{1} & \mathit{7} \\
6 & 1 & 7 & \mathbf{2} & 8 & 3 & 9 & 5 & 0 \\
\hline\hline
8 & 3 & 9 & 5 & \mathbf{0} & 6 & 1 & 7 & 2 \\
5 & 0 & 6 & 1 & 7 & \mathbf{2} & 8 & 3 & 9 \\
1 & 7 & 2 & 8 & 3 & 9 & 5 & 0 & 6 \\
\hline
\end{array}
$$

Notably, in each 3-row section, the same three 3-digit numbers rotate:

$$
\begin{aligned}
		\mathbf{950} \space \mathit{617} \space 283 \\
		283 \space \mathbf{950} \space \mathit{617} \\
		\mathit{617} \space 283 \space \mathbf{950} \\
\end{aligned}
$$

Also, the $\gcd$ ended up being $12345679$!

### Answer

Thus, the final answer to this month’s puzzle is $283950617$.


---

### About Me

I am currently a Sophomore at Northeastern University studying Computer Science and Mathematics, with a goal to work in the quantitative finance field. I love working through these type of problems and using code to make the solution more elegant. More information on me and some projects I'm proud of can be found on my website below.

[Personal Website](https://www.maxcyrusmayer.com)
[LinkedIn](https://www.linkedin.com/in/max-mayerr/)
[Contact Me](mailto:max@maxcyrusmayer.com)
