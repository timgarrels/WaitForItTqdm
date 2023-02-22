# WaitForItTqdm
An iterable which either shows a progress bar on each iteration, or on one specific one.

I had a list and an algorithm implemented by a third party libraray. The algorithm would take a long time to compute, so I wanted a progress bar:
```python
from third_party import algorithm
from tqdm import tqdm

elements = [1, 2, 3, 4, 5]

algorithm(tqdm(elements))
```

However, as it turns out, the algorithm does some preprocessing which looks like this:
```python

def algorithm(elements: List[int):
  # Preprocessing (fast to compute)
  for e in elements:
    preprocess(e)
  
  # Core Algorithm (heavy-lifting, slow)
  for e in elements:
    process(e)
  
```

This means the process-bar goes to 100% in the first iteration (preprocessing) and then does not reset for the next iteration, which is the more interesting one to keep track of.

Of course, you could edit your third-party lib to use `tqdm` by itself, but who wants to edit third party libraries?


Instead, you can use the `WaitForItTqdm` class to do this: In the above example, we know that there are two iterations over `elements` and the second one is the important one. So we can write
```python
from third_party import algorithm
from tqdm import tqdm

from WaitForItTqdm import WaitForItTqdm

elements = [1, 2, 3, 4, 5]

algorithm(WaitForItTqdm(elements, specific_pass=2, desc="ALgorithm Heavy Lifting"))

```

This way, a `tqdm` progessbar is created only on the second (`specific_pass=2`) iteration over the wrapped list.
