# Python Clean Code Guidelines

This is a guide titled "Python Clean Code Guidelines", prepared for Python developers (especially those working in the field of artificial intelligence). The content is based on personal experience as well as insights from friends and reputable sources. Its goal is to help write readable, maintainable, professional code that follows best practices. There is always room for improvement, and suggestions are warmly welcomed. It is obvious that the most ideal recommendations may not always be feasible, yet adhering to them brings undeniable benefits.

## Table of Contents

1. [Rules for Style Guides](#rules-for-style-guides)
2. [Rules for Naming](#rules-for-naming)
3. [Rules Related to Functions](#rules-related-to-functions)
4. [Rules Related to Comments](#rules-related-to-comments)
5. [Rules Related to Objects and Data Structures](#rules-related-to-objects-and-data-structures)
6. [Rules Related to Error Handling](#rules-related-to-error-handling)
7. [Rules Related to Classes](#rules-related-to-classes)
8. [Rules for Using Type Hints and Type Checking (Optional)](#rules-for-using-type-hints-and-type-checking-optional)
9. [Rules Related to Using AI in Software Development](#rules-related-to-using-ai-in-software-development)
10. [Recommendations](#recommendations)

---

## Rules for Style Guides

For the Python language, there are various style guides, with PEP 8 being particularly popular and widely adopted in the AI programming community. The team aims to adhere to this style guide. PEP 8 contains many detailed rules that can be reviewed on its [official site](https://peps.python.org/pep-0008/). This document will not provide an exhaustive explanation of them. Typically, we rely on tools integrated into the IDE to ensure PEP 8 compliance.

### Using Flake8

If you are using Flake8, proceed as follows:

```bash
pip install flake8
python -m flake8 app.py
```

### IDE Settings

- **PyCharm Settings:** Go to Settings, search for "Inspections", and then search for "PEP8" within the Inspections section. Enable the two PyCharm inspection features related to PEP 8; from then on, non-compliant code sections will be highlighted with yellow underlines.

- **VS Code Settings:** For other popular editors such as VS Code or Sublime Text, relevant extensions or packages are usually available through their respective package managers.

### Sample Flake8 Output

```
app.py:58:20: W605 invalid escape sequence '\.'
app.py:68:80: E501 line too long (125 > 84 characters)
app.py:79:9: W291 trailing whitespace
app.py:88:1: W293 blank line contains whitespace
app.py:96:5: F841 local variable 'temp_result' is assigned to but never used
app.py:104:1: F401 'numpy as np' imported but unused
app.py:115:13: E402 module level import not at top of file
app.py:123:20: E701 multiple statements on the same line (colon)
app.py:135:10: E203 whitespace before ':'
app.py:150:25: W503 line break before binary operator
app.py:160:17: E722 do not use bare 'except'
```

In the future, we can configure CI/CD pipelines to run flake8 checks before commits or pushes, allowing the operation only if the code passes.

### Simple Examples for PEP8

The following examples are intentionally not fully PEP 8 compliant. Your code will be checked against all PEP 8 rules; these are provided only to build familiarity.

- Package, module, function, and variable names should use `snake_case` (lowercase letters with words separated by underscores).
- Class names should use `PascalCase`. Acronyms within PascalCase should be fully uppercase (e.g., `MBAService`).
- Constants are typically defined at module level but are preferably moved to a configuration file. In any case, they are written in uppercase with words separated by underscores.

```python
THIS_IS_A_CONSTANT
```

- Line length should not exceed 79 characters to ensure readability in any environment.

---

## Rules for Naming

This section is one of the most challenging yet most important aspects of clean code. Many of these practices require building a cultural habit, but with consistent practice, they become second nature.

- Use names that convey exactly the intent you have in mind, so others understand the same concept.
- Avoid misleading names.
- Make meaningful distinctions—names should help the reader understand the code better.
- Choose pronounceable names.
- Avoid extremely short or overly generic names (e.g., `my_list`, `data`, `info_map`) as well as excessively long ones (e.g., `dictionary_for_the_purpose_of_storing_data`).
- Use searchable names. Single-letter names like `a` are poor choices because they appear in many unrelated contexts.
- Avoid encodings (e.g., Hungarian notation).
- Class names should be nouns or noun phrases (e.g., `Customer`, `Account`, `AddressParser`).
- Method names should be verbs or verb-containing phrases (e.g., `post_payment`, `delete_page`).
- **Use meaningful names for boolean variables:** Prefer names that read as yes/no questions rather than generic `flag` or `status`:

```python
# Bad
flag = True
status = False

# Good
is_active = True
has_permission = False
can_edit = True
should_retry = False
```

- Avoid humor in naming—jokes may not translate or could mislead others.
- Add context where needed to prevent ambiguity. A variable named `state` inside a method may be unclear; adding context clarifies its meaning.
- Do not include obvious or redundant information (e.g., prefixing every class with "GSD" for a project named Gas Station Deluxe).

---

## Rules Related to Functions

- **Keep them small!** This is perhaps the most important principle. Functions should generally not exceed 20 lines. If longer, break them into smaller, meaningful units. This greatly simplifies debugging and helps internalize program flow.

- **Do one thing only!** This clearly defines responsibility; if inputs/outputs are unexpected, you know exactly where to debug.

- **One level of abstraction per function.** Organize code hierarchically—higher-level functions call lower-level ones, each handling a single abstraction level.

- **Use descriptive names** that clearly indicate what the function does.

- **Avoid mutable default arguments** (e.g., lists or dictionaries):

```python
# Bad
def add_item(item, items=[]):
    items.append(item)
    return items

# Good
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

- **Prefer exceptions over error codes.** Use meaningful exceptions instead of returning error enums or codes.

- **Don't Repeat Yourself (DRY).** Extract duplicated logic into reusable functions to reduce maintenance effort.

- **Structured programming.** Follow Dijkstra's rule: each function should have a single entry and single exit point. Avoid `goto`, and minimize `break`/`continue`.

- **Use decorators for cross-cutting concerns** such as logging, timing, caching, or validation:

```python
from functools import wraps
import time

def timing_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f} seconds")
        return result
    return wrapper

@timing_decorator
def slow_function():
    time.sleep(2)
```

- **Limit function parameters** (ideally 3–4 maximum). For more, use a dataclass or TypedDict:

```python
from dataclasses import dataclass

@dataclass
class UserConfig:
    name: str
    email: str
    age: int
    is_active: bool

def create_user(config: UserConfig) -> User:
    # Instead of 4 separate parameters
    pass
```

---

## Rules Related to Comments

- **Good comments do not rescue bad code.** Long functions with poor abstraction cannot be saved by extra comments.

- **Express yourself in code, not comments.**

- **Use TODO comments** when a feature must be postponed. Describe the task clearly. Coordinated TODOs are acceptable and appreciated; both the author and reviewer should verify them before commit/push.

- **Avoid unnecessary, misleading, or journal-style comments** (e.g., historical notes belong in commit messages).

- **Prefer code over comments** when possible:

```python
# Bad
# Check if employee is eligible for retirement
if employee.age > 65 and employee.years_of_service > 20:
    pass

# Good
def is_eligible_for_retirement(employee: Employee) -> bool:
    return employee.age > 65 and employee.years_of_service > 20

if is_eligible_for_retirement(employee):
    pass
```

- **Avoid position markers** (e.g., `#### Actions ####`) when code is naturally readable.

- **Do not commit commented-out code.** It clutters the file and distracts reviewers.

- **Use docstrings instead of inline comments** for functions, classes, and modules:

```python
def calculate_discount(price: float, discount_percent: float) -> float:
    """
    Calculate the final price after applying the discount.
    
    Args:
        price: The original product price
        discount_percent: Discount percentage (0 to 100)
        
    Returns:
        Final price after applying the discount
        
    Raises:
        ValueError: If discount_percent is outside the 0-100 range
        
    Examples:
        >>> calculate_discount(1000, 10)
        900.0
    """
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount percentage must be between 0 and 100")
    return price * (1 - discount_percent / 100)
```

- **Use documentation tools** such as Sphinx or MkDocs to generate HTML documentation automatically from docstrings.

---

## Rules Related to Objects and Data Structures

- **Object/Data Asymmetry:** Objects hide implementation details behind abstractions and expose behavior through methods.

- **Law of Demeter:** A method of an object should only call methods on its own object, objects it creates, objects passed as arguments, or objects it holds as instance variables.

- **Data Transfer Objects (DTOs):** Simple classes containing only public data attributes (no behavior), commonly used for transferring data (e.g., from APIs).

- **Use `__slots__`** when dynamic attribute addition is unnecessary—it reduces memory usage and improves access speed. (See Python documentation for details on `__slots__`.)

- **Pattern Matching (Python 3.10+):** Useful for handling complex data structures:

```python
def process_response(response):
    match response:
        case {"status": 200, "data": data}:
            return f"Success: {data}"
        case {"status": 404}:
            return "Not found"
        case {"status": code, "error": msg}:
            return f"Error {code}: {msg}"
        case _:
            return "Invalid response"
```

---

## Rules Related to Error Handling

- **Prefer exceptions over error codes.**

- **Use the narrowest possible `try` scope.**

- **Provide rich exception information** to pinpoint the source of errors.

- **Define custom exceptions based on caller needs.**

- **Structure normal flow clearly** so exceptions stand out.

- **Avoid returning `None` for failure**—raise an exception instead; `None` is ambiguous.

- **Use logging instead of print statements:**

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

try:
    result = risky_operation()
except ValueError as e:
    logger.error(f"Error in operation: {e}", exc_info=True)
    raise
```

- **Use ExceptionGroup (Python 3.11+)** for concurrent error handling:

```python
try:
    raise ExceptionGroup("Multiple errors occurred", [
        ValueError("Value error"),
        TypeError("Type error"),
    ])
except* ValueError as e:
    print(f"Handling value errors: {e}")
except* TypeError as e:
    print(f"Handling type errors: {e}")
```

---

## Rules Related to Classes

- **Keep classes small.** Apply the same principles as for functions, plus careful responsibility management.

- **Single Responsibility Principle (SRP):** Each class or module should have only one reason to change.

- **Design for change.** Clean systems minimize the risk and cost of modifications.

- **Use Abstract Base Classes (ABC):**

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, amount: float) -> bool:
        pass
    
    @abstractmethod
    def refund(self, transaction_id: str) -> bool:
        pass

class StripePaymentProcessor(PaymentProcessor):
    def process_payment(self, amount: float) -> bool:
        # Implementation
        pass
    
    def refund(self, transaction_id: str) -> bool:
        # Implementation
        pass
```

- **Dependency Injection:** Inject dependencies rather than creating them internally:

```python
# Bad
class UserService:
    def __init__(self):
        self.db = DatabaseConnection() # Hard-coded

# Good
class UserService:
    def __init__(self, db: DatabaseConnection):
        self.db = db # Injected
```

- **Prefer composition over inheritance:**

```python
class Logger:
    def log(self, message: str) -> None:
        print(message)

class EmailSender:
    def send(self, to: str, message: str) -> None:
        print(f"Sending to {to}: {message}")

class UserManager:
    def __init__(self, logger: Logger, email_sender: EmailSender):
        self.logger = logger
        self.email_sender = email_sender
    
    def create_user(self, email: str) -> None:
        self.logger.log(f"Creating user: {email}")
        self.email_sender.send(email, "Welcome!")
```

---

## Rules for Using Type Hints and Type Checking (Optional)

One of the most significant recent improvements in Python is the introduction of type hints (PEP 484 and later).

- **Use type hints everywhere** for functions, methods, and important variables to improve readability, catch errors early, and enhance IDE support:

```python
def calculate_total(price: float, quantity: int) -> float:
    return price * quantity

user_name: str = "Ali"
user_scores: list[int] = [85, 90, 78]
```

- **Use Optional and Union (or the new syntax):**

```python
from typing import Optional, Union

def find_user(user_id: int) -> Optional[User]:
    pass

def process_data(data: Union[str, bytes]) -> str:
    pass

# Python 3.10+
def find_user(user_id: int) -> User | None:
    pass
```

---

## Rules Related to Using AI in Software Development

- **Smart use of AI assistants** (GitHub Copilot, ChatGPT, Claude, Grok, etc.) can dramatically boost productivity for:
  - Writing boilerplate code
  - Generating unit tests
  - Refactoring
  - Writing docstrings
  - Explaining complex code

- **AI Code Review:** Before submitting a Pull Request, ask an AI to review your code and suggest improvements.

- **Automatic test generation:** Ask AI to produce comprehensive test cases, including edge cases.

```python
# Example prompt to AI:
# "Write various tests for this function that also cover edge cases"
def calculate_discount(price: float, discount_percent: float) -> float:
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount percentage must be between 0 and 100")
    return price * (1 - discount_percent / 100)
```

- **Learning patterns and best practices** with AI assistance.

- **Know AI limitations:**
  - Always manually review AI-generated code
  - AI can introduce bugs or security issues
  - Do not rely on AI for critical architectural decisions
  - Use AI as a learning tool, not a full replacement for expertise

- **Prompt engineering matters:** Clear, detailed prompts yield better results:

```
# Bad prompt
"Improve this code"

# Good prompt
"Refactor this function to:
1. Add type hints
2. Implement proper exception handling
3. Include a Google-style docstring
4. Follow SOLID principles"
```

---

## Recommendations

- When working with a powerful IDE like those from JetBrains, it's essential to become familiar with its features and keyboard shortcuts. This significantly boosts your speed in implementing ideas, testing code, and resolving questions that arise during development. This leads to producing higher-quality code much more efficiently, and the time saved can be invested in further learning.

- **requirements.txt:** It's worth repeating this tip countless times—always maintain a `requirements.txt` file for your projects. Ideally, have separate ones for environments like Production, Development, and Test. At minimum, start with one.

- When adding a new component or similar to a project, ensure there's a Code Reviewer. Having your code reviewed before it reaches the Development stage means it gets to production faster, with smoother transitions and less time wasted on fixes.

---
