#### Функция поиска почты в строке
```python
def email_finder(string):             # определим функцию, она принимает на вход 1 аргумент — строку
    list_of_words = string.split(' ') # разобьем то, что функция получила на вход на слова
    for word in list_of_words:        # проитерируем список со словами
        if word.find('@') > 0:        # если в слове есть символ @ 
            return word               # заканчиваем выполнение функции и возвращаем это слово
    return 'Почт нет'                  # если же функция отработала до конца, и не нашла @ — возвращаем сообщение "Почт нет"
    ```
