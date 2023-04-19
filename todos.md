Radon

- Quiz questions to calculate code complexity for certain blocks

```bash
radon cc cc.py --show-complexity 

cc.py
    F 1:0 report_driving_status_for_age - A (5)
    F 13:0 report_driving_and_drinking_status_for_age - A (4)
```

```python
def report_driving_status_for_age(age: int) -> None:
    if age < 0:
        print("You are not born yet.")
    elif age <= 15:
        print("You may be old enough to get a drivers' permit in some U.S. states.")
    elif age <= 17:
        print("You may be old enough to get a drivers' license in most U.S. states.")
    elif age <= 18:
        print("You may be old enough to get a drivers permit in all U.S. states.")
    else:
        print("You are old enough to get a drivers permit in all U.S. states.")
```

```python
def report_driving_and_drinking_status_for_age(age: int) -> None:
    if age >= 16 and age < 21:
        print("You are old enough to drive but not old enough to drink.")
    elif age >= 21:
        print("You are old enough to drive and old enough to drink.")
    else:
        print("You are not old enough to drive or drink.")
```
