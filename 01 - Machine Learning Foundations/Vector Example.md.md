## Worked Example: Netflix Movie Recommendation

Let's see how vectors, matrices, and matrix multiplication are used in a real-world application.

Imagine Netflix has thousands of movies.

To help recommend movies, Netflix represents each movie using a vector of features.

Suppose we use these four genres:

```text
[Action, Comedy, Romance, Sci-Fi]
```

Each number represents **how strongly the movie belongs to that genre**, on a scale from **0 to 5**.

- **0** = Not present
- **5** = Very strong

### Step 1: Represent Movies as Vectors

Movie A:

```text
[5, 1, 0, 4]
```

Meaning:

- Action = 5 (Very High)
- Comedy = 1 (Low)
- Romance = 0 (None)
- Sci-Fi = 4 (High)

Movie B:

```text
[1, 5, 3, 0]
```

Meaning:

- Action = 1 (Low)
- Comedy = 5 (Very High)
- Romance = 3 (Medium)
- Sci-Fi = 0 (None)

Movie C:

```text
[4, 0, 1, 5]
```

Meaning:

- Action = 4 (High)
- Comedy = 0 (None)
- Romance = 1 (Low)
- Sci-Fi = 5 (Very High)

### Step 2: Stack the Movie Vectors

When we stack all movie vectors together, we get a **Movies Matrix**.

```text
Movies Matrix

5  1  0  4
1  5  3  0
4  0  1  5
```

Each row represents one movie.

### Step 3: Represent the User

Suppose a user has the following preferences:

```text
User = [4, 1, 0, 5]
```

This means:

- Likes Action = 4
- Likes a little Comedy = 1
- Doesn't like Romance = 0
- Loves Sci-Fi = 5

### Step 4: Compute Recommendation Scores

Now multiply the Movies Matrix by the User Vector.

```python
import numpy as np

movies = np.array([
    [5, 1, 0, 4],
    [1, 5, 3, 0],
    [4, 0, 1, 5]
])

user = np.array([4, 1, 0, 5])

scores = movies @ user

print(scores)
```

Output:

```text
[41  9 41]
```

*(The exact values depend on the movie vectors you choose. The important idea is how the scores are computed.)*

### Step 5: Understanding the Scores

Python compares the user's preferences with **every movie at once**.

For example:

- Movie A → 41
- Movie B → 9
- Movie C → 41

Higher score = Better recommendation.

Movie B receives a much lower score because it contains genres the user doesn't strongly prefer.

### Why Matrix Multiplication?

Without matrix multiplication, we'd compare the user with each movie individually:

```text
Movie A • User
Movie B • User
Movie C • User
```

With matrix multiplication:

```python
movies @ user
```

Python performs **all those comparisons in one operation**.

This is why recommendation systems can compare users against **millions of movies** in just milliseconds.

---

### Key Takeaway

This simple example demonstrates one of the most important ideas in Machine Learning:

- A movie is represented as a **vector**.
- All movies together form a **matrix**.
- A user's preferences are another **vector**.
- Matrix multiplication computes similarity scores for **all movies simultaneously**.

This same mathematical operation is used throughout AI, including recommendation systems, neural networks, and Transformers.