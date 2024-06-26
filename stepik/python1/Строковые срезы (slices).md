[[python_learn1]]
Рассмотрим строку `s = 'abcdefghij'`.
С помощью среза мы можем получить несколько символов исходной строки, создав диапазон индексов, разделенных двоеточием `s[x:y]`.

Следующий программный код:

```python
print(s[2:5])
print(s[0:6])
print(s[2:7])
```

выводит:

```no-highlight
cde
abcdef
cdefg
```
При построении среза `s[x:y]` первое число – это то место, где начинается срез (**включительно**), а второе – это место, где заканчивается срез (**невключительно**). Разрезая строки, мы создаем подстроку, которая по сути является строкой внутри другой строки.

### Срез до конца, от начала

Если опустить второй параметр в срезе `s[x:]` (но поставить двоеточие), то срез берется до конца строки. Аналогично если опустить первый параметр `s[:y]`, то можно взять срез от начала строки. Срез `s[:]` совпадает с самой строкой `s`.

Следующий программный код:

```python
print(s[2:])
print(s[:7])
```

выводит:

```no-highlight
cdefghij
abcdefg
```
### Отрицательные индексы в срезе
![[Pasted image 20231010171938.png]]

### Шаг среза

![[Pasted image 20231010172026.png]]

Если в качестве шага среза указать **отрицательное число**, то символы будут идти в обратном порядке.

Следующий программный код:

```python
print(s[::-1])
```

выводит:

```no-highlight
jihgfedcba
```

Следующий программный код:

```python
print(s[1:7:2])
print(s[3::2])
print(s[:7:3])
print(s[::2])
print(s[::-1])
print(s[::-2])
```

выводит:

```no-highlight
bdf
dfhj
adg
acegi
jihgfedcba
jhfdb
```
![[Pasted image 20231010172118.png]]
## Изменение символа строки по индексу

Предположим, у нас есть строка `s = 'abcdefghij'` и мы хотим заменить символ с индексом 44 на `'X'`. Можно попытаться написать код:

```python
s[4] = 'X'
```

Однако такой код не работает. В Python строки являются **неизменяемыми**, то есть мы не можем менять их содержимое с помощью индексатора.

Если мы хотим поменять какой-либо символ строки `s`, мы должны создать новую строку. Следующий код использует срезы и решает поставленную задачу:

```python
s = s[:4] + 'X' + s[5:]
```

	Мы создаем два среза: от начала строки до 55-го символа (не включительно) и с 66-го символа (включительно) по конец строки, а между ними вставляем нужный нам символ, который встанет на 55-ю позицию (44 индекс).