### Функция отчистки номера телефона от всех символов, кроме цифр
```python
def phone_cleaner(tel):
    clear_phone = ''
    for digit in tel:
        if digit in '1234567890':
            clear_phone += digit
    return clear_phone
    ```
