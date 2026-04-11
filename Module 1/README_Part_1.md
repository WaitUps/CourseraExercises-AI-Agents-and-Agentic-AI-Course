This notebook serves as a practical guide for interacting with Google's GenAI models using the Python SDK. It demonstrates a **"Quasi-Agentic" workflow** by maintaining a persistent message memory, allowing the LLM to build upon previous outputs to refine code, add documentation, and generate a testing suite.

---

## Part 1: Iterative Development via Programmatic Prompting

### Execution Environment & Model Details
- **Model:** `gemini-flash-lite-latest`
- **Configuration:** `{"max_output_tokens": 4096}`
- **Core Library:** `google-genai` (Gemini API)
- **Architecture:** Iterative state management using a JSON-based message history.

---

## STEP 1: Generating Initial Function
**Goal:** Establish the functional foundation of the converter.

### Prompt for Step 1:
```text
Write a basic Python function that converts a decimal integer into its binary, octal, and hexadecimal string representations..
```

### Messages (Memory) BEFORE Step 1:
```json
[
  {
    "role": "user",
    "content": "You are a Python expert helping to develop a function. Always output code within a ```python code block```."
  }
]
```

### LLM Output (Initial Logic):
To convert a decimal integer into binary, octal, and hexadecimal strings, Python provides built-in functions: `bin()`, `oct()`, and `hex()`. 

```python
def convert_number_systems(n):
    """
    Converts a decimal integer into binary, octal, and hexadecimal strings.
    Note: Python's built-in functions include prefixes (0b, 0o, 0x).
    """
    return {
        "decimal": n,
        "binary": bin(n),
        "octal": oct(n),
        "hexadecimal": hex(n)
    }

# Example usage:
result = convert_number_systems(255)
print(result)
```

### Messages (Memory) AFTER Step 1:
```json
[
  {
    "role": "user",
    "content": "You are a Python expert helping to develop a function. Always output code within a ```python code block```."
  },
  {
    "role": "user",
    "content": "Write a basic Python function that Converts a decimal integer into its binary, octal, and hexadecimal string representations.."
  },
  {
    "role": "model",
    "content": "def convert_number_systems(n):\n    \"\"\"\n    Converts a decimal integer into binary, octal, and hexadecimal strings.\n    Note: Python's built-in functions include prefixes (0b, 0o, 0x).\n    \"\"\"\n    return {\n        \"decimal\": n,\n        \"binary\": bin(n),\n        \"octal\": oct(n),\n        \"hexadecimal\": hex(n)\n    }\n\n# Example usage:\nresult = convert_number_systems(255)\nprint(result)"
  }
]
```

---

## STEP 2: Adding Documentation
**Goal:** Refine the logic by adding professional documentation and edge-case descriptions.

### Prompt for Step 2:
```text
Now, add comprehensive documentation to this function, including a description, parameter descriptions, return value description, example usage, and edge cases. Make sure the output is just the updated function in a ```python code block```.
```

### LLM Output (Documented Function):
```python
def convert_number_systems(n):
    """
    Converts a decimal integer into its binary, octal, and hexadecimal string representations.

    This function utilizes Python's built-in conversion functions. Note that the 
    returned strings include the standard Python prefixes: '0b' for binary, 
    '0o' for octal, and '0x' for hexadecimal.

    Parameters:
    n (int): The decimal integer to be converted.

    Returns:
    dict: A dictionary containing the original integer and its string 
          representations with the following keys:
          - 'decimal': The original integer.
          - 'binary': The binary string representation.
          - 'octal': The octal string representation.
          - 'hexadecimal': The hexadecimal string representation.

    Example:
    >>> convert_number_systems(255)
    {'decimal': 255, 'binary': '0b11111111', 'octal': '0o377', 'hexadecimal': '0xff'}

    Edge Cases:
    - If n is 0, returns {'decimal': 0, 'binary': '0b0', 'octal': '0o0', 'hexadecimal': '0x0'}.
    - If n is negative, the prefixes are preceded by a minus sign (e.g., -0b101).
    - If input is not an integer, a TypeError will be raised by the built-in conversion functions.
    """
    return {
        "decimal": n,
        "binary": bin(n),
        "octal": oct(n),
        "hexadecimal": hex(n)
    }
```

### Messages (Memory) AFTER Step 2:
```json
[
  {
    "role": "user",
    "content": "You are a Python expert helping to develop a function. Always output code within a ```python code block```."
  },
  {
    "role": "user",
    "content": "Write a basic Python function that Converts a decimal integer into its binary, octal, and hexadecimal string representations.."
  },
  {
    "role": "model",
    "content": "def convert_number_systems(n):\n    \"\"\"\n    Converts a decimal integer into binary, octal, and hexadecimal strings.\n    Note: Python's built-in functions include prefixes (0b, 0o, 0x).\n    \"\"\"\n    return {\n        \"decimal\": n,\n        \"binary\": bin(n),\n        \"octal\": oct(n),\n        \"hexadecimal\": hex(n)\n    }\n\n# Example usage:\nresult = convert_number_systems(255)\nprint(result)"
  },
  {
    "role": "user",
    "content": "Now, add comprehensive documentation to this function, including a description, parameter descriptions, return value description, example usage, and edge cases. Make sure the output is just the updated function in a ```python code block```."
  },
  {
    "role": "model",
    "content": "def convert_number_systems(n):\n    \"\"\"\n    Converts a decimal integer into its binary, octal, and hexadecimal string representations.\n\n    This function utilizes Python's built-in conversion functions. Note that the \n    returned strings include the standard Python prefixes: '0b' for binary, \n    '0o' for octal, and '0x' for hexadecimal.\n\n    Parameters:\n    n (int): The decimal integer to be converted.\n\n    Returns:\n    dict: A dictionary containing the original integer and its string \n          representations with the following keys:\n          - 'decimal': The original integer.\n          - 'binary': The binary string representation.\n          - 'octal': The octal string representation.\n          - 'hexadecimal': The hexadecimal string representation.\n\n    Example:\n    >>> convert_number_systems(255)\n    {'decimal': 255, 'binary': '0b11111111', 'octal': '0o377', 'hexadecimal': '0xff'}\n\n    Edge Cases:\n    - If n is 0, returns {'decimal': 0, 'binary': '0b0', 'octal': '0o0', 'hexadecimal': '0x0'}.\n    - If n is negative, the prefixes are preceded by a minus sign \n      (e.g., -0b101).\n    - If input is not an integer, a TypeError will be raised by the \n      built-in conversion functions.\n    \"\"\"\n    return {\n        \"decimal\": n,\n        \"binary\": bin(n),\n        \"octal\": oct(n),\n        \"hexadecimal\": hex(n)\n    }"
  }
]
```

---

## STEP 3: Adding Test Cases
**Goal:** Generate a verification suite based on the context of the documented function.

### Prompt for Step 3:
```text
Finally, add unittest test cases for this function. Tests should cover basic functionality, edge cases, and various input scenarios. Output the test cases code in a separate ```python code block```.
```

### LLM Output (Test Suite):
```python
import unittest

class TestConvertNumberSystems(unittest.TestCase):

    def test_positive_integer(self):
        result = convert_number_systems(10)
        self.assertEqual(result["binary"], "0b1010")
        self.assertEqual(result["octal"], "0o12")
        self.assertEqual(result["hexadecimal"], "0xa")

    def test_zero(self):
        result = convert_number_systems(0)
        self.assertEqual(result["binary"], "0b0")
        self.assertEqual(result["octal"], "0o0")
        self.assertEqual(result["hexadecimal"], "0x0")

    def test_negative_integer(self):
        result = convert_number_systems(-10)
        self.assertEqual(result["binary"], "-0b1010")
        self.assertEqual(result["octal"], "-0o12")
        self.assertEqual(result["hexadecimal"], "-0xa")

    def test_large_integer(self):
        result = convert_number_systems(1024)
        self.assertEqual(result["binary"], "0b10000000000")
        self.assertEqual(result["hexadecimal"], "0x400")

    def test_invalid_input(self):
        with self.assertRaises(TypeError):
            convert_number_systems("string")
        with self.assertRaises(TypeError):
            convert_number_systems(None)

if __name__ == '__main__':
    unittest.main()
```

### Messages (Memory) AFTER Step 3:
```json
[
  // ... previous messages (Step 0-2) ...
  {
    "role": "user",
    "content": "Finally, add unittest test cases for this function. Tests should cover basic functionality, edge cases, and various input scenarios. Output the test cases code in a separate ```python code block```."
  },
  {
    "role": "model",
    "content": "import unittest\n\nclass TestConvertNumberSystems(unittest.TestCase):\n\n    def test_positive_integer(self):\n        result = convert_number_systems(10)\n        self.assertEqual(result[\"binary\"], \"0b1010\")\n        self.assertEqual(result[\"octal\"], \"0o12\")\n        self.assertEqual(result[\"hexadecimal\"], \"0xa\")\n\n    def test_zero(self):\n        result = convert_number_systems(0)\n        self.assertEqual(result[\"binary\"], \"0b0\")\n        self.assertEqual(result[\"octal\"], \"0o0\")\n        self.assertEqual(result[\"hexadecimal\"], \"0x0\")\n\n    def test_negative_integer(self):\n        result = convert_number_systems(-10)\n        self.assertEqual(result[\"binary\"], \"-0b1010\")\n        self.assertEqual(result[\"octal\"], \"-0o12\")\n        self.assertEqual(result[\"hexadecimal\"], \"-0xa\")\n\n    def test_large_integer(self):\n        result = convert_number_systems(1024)\n        self.assertEqual(result[\"binary\"], \"0b10000000000\")\n        self.assertEqual(result[\"hexadecimal\"], \"0x400\")\n\n    def test_invalid_input(self):\n        with self.assertRaises(TypeError):\n            convert_number_systems(\"string\")\n        with self.assertRaises(TypeError):\n            convert_number_systems(None)\n\nif __name__ == '__main__':\n    unittest.main()"
  }
]
```

---

## 💡 Important Takeaways for AI Engineering

1.  **Iterative State Management:** The success of the response depends on feeding the model its own previous answers. This mimics how an AI Agent maintains "state."
2.  **Persona Consistency:** Setting the persona in the first message (`"You are a Python expert"`) ensures that all subsequent steps (documentation, testing) adhere to professional standards.
3.  **Role Conversion:** The Gemini API `generate_content` method does not use a dedicated 'system' role. "System" instructions must be converted to the first 'user' message to set the persona.
4.  **Edge Case Prompting:** Explicitly asking for "edge cases" in Step 2 forced the model to consider negative integers and zero, providing a shortcut to more robust code.
5.  **Programmatic Utility:** When asking for updates, specifying that the output should be *just* the code block reduces "chatter" and makes the result directly usable in automation scripts.
