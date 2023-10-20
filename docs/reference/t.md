# 1. Text Multiplication Guide

### What is a Text?

The basic object in LangTorch is `langtorch.TextTensor`, corresponding to `torch.nn.Tensor`. While `nn.Tensor` is a tensor of numbers, the `TextTensor` is a tensor of `Text` objects. Text objects are not simply strings to allow them to represent more complex objects and undergo more complex operations (e.g. multiplication, which will be a composition operation for Text objects that acts similar to the string .format method).  which are defined by a tuple `(content, key)` where content and key are regular strings.

## **1. Basics:**

### **Text:**

- Represents a sequence of "named strings". It acts as a regular string instance, but provides additional methods to manipulate the named strings within.
- The class provides multiple ways of creating instances, either through direct invocation or using patterns. That will be in the next chapter.
- The `Text` class can be initialised in multiple ways. This section focuses on constructing Text from strings, lists and dictionaries. A more convenient way is to use a sepcial f-string-like sytnax which can parse most Text’s from a signle string. This is explained in the next chapter.

```python
# Creating a Text object with (key,  value) syntax:
text_obj = Text(("greeting", "Hello"), ("object", "world"))
prompt = TextTensor(["{object} says: {greating}"}])
print(text_obj)  # Outputs: Helloworld
print(prompt*text_obj)  # Outputs: world says: Hello

# Tho get substrings of the Text, the content attribute holds sub-Texts:
print(text_obj.content)  # Outputs: [Text(("greeting", "Hello")), Text(("object", "world"))]

# Accessing the items (key-value pairs) of a Text object:
items = text_obj.items()
print(items)  # Outputs: [("greeting", "Hello"), ("object", "world")]
```

## **2. Operations on Text:**

### **Addition Operation:**

The addition operation on `Text` and `TextTensors` simply concatenates them.

```python
# Concatenating two Text objects:
text1 = Text(("greeting", "Hello"))
text2 = Text(("object", "world"), "!")
result_text = text1 + text2
print(result_text)  # Outputs: Helloworld!

# Mixing str2 and Text:
mix_result = str1 + text2
print(mix_result)  # Outputs: Helloworld!
```

### **Multiplication Operation:**

The heart of this system is the multiplication operation, defined in the `__mul__` method of both classes. Here's how it works:

### Single-key Text **Multiplication:**

- `str2 * str2`: When two Text objects are multiplied:
    - If their keys match but not their contents, the contents get concatenated.
    - If one is the inverse of the other (content and key swapped), the identity (an empty string) is returned.
    - If the content of the first `str2` matches the key of the second it acts as a format operation.
    For example:
        
        ```python
         Text(("","key")) * Text("key","value") == Text(("", "value"))
        ```
        
        You can interpret the whole operation like a multiplication of rational numbers:
        
        $$
        
        \frac{\text{'It works'}}{\text{'a key'}}\circ\frac{\text{ ' like this'}}{\text{'a key'}} = \frac{\text{'It works like this'}}{\text{'a key'}}\\ \text{and}\\
        
        \frac{\text{'a phrase'}}{\text{''}}\circ\frac{\text{ 'text'}}{\text{'a phrase'}} = \frac{\text{'text'}}{\text{''}}
        $$
        
    - For different keys, a new `Text` object is created with one `str2` instance after the other.
- `str2 * str`: If a `str2` object is multiplied with a regular string:
    - If the `str2` doesn't have a key, the regular string is simply concatenated to its content.
    - Otherwise, a new `Text` object is created with the `str2` followed by the regular string.

### Longer **Text Multiplication:**

- `Text * str2`: The str2 goes from left to right and if it finds a match for any of the rules above it applies it to that substring of Text, if none such match is found it appends itself to the Text
- `Text * Text`: similar but for every in the right Text, it becomes clearer when we introduce creating Texts from strings

### **Inverse Operation:**

The inverse of a Text is obtained by swapping the key and content. To get the inverse of a Text you can either use the `inv()` method or the power operation `**-1`. The Text multiplied by its inverse is the empty string (`Text.identity`), as shown below.

```python
# For Text:
text_obj = Text(("Hello", "greeting"), ("world", "object"))
# text_obj.inv() == text_obj**-1 == Text(("greeting", "Hello"), 
#																				 ("object", "world"))

# We have:
# text_obj*(text_obj**-1) == Text.identity == ""
```

## **4. Additional Points:**

- The `Text` class has a a special value `Text.identity`: an empty string with no key. This value is characterised by being it being the identity element of multiplication and  `Text.identity == 1` is defined to be true to make that connection, as:
    
    ```python
    some_text * 1 == some_text * Text.identity
    ```