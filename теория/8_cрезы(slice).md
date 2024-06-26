### Срезы / Слайсы

Слайсы в Go является ссылочным типом данных.

Слайсы — это те же "массивы", но переменной длины.
Слайсы в Go уже являются ссылочными типами данных, поэтому, в большинстве случаев, получение адреса слайса не требуется.

Срезы (slice) представляют последовательность элементов ОДНОГО типа переменной длины.

### Oсобенности:

- **Динамическое изменение размера:** <br>
Слайсы могут ИЗМЕНЯТЬ свой размер ДИНАМИЧЕСКИ. <br>
При создании слайса вы указываете только его начальную емкость (capacity), и слайс может автоматически расширяться при добавлении элементов.
```go
    var slice []int // не-инициализированный слайс, значение ссылки на объект nil
	//или
    slice := []int(nil)
	//или
    slice := []int{}
    //вывод: [], длина 0, емкость 0
	
    slice := []int{1, 2, 3, 4, 5} //Использование литерала слайса
	//или
    var slice := []int{1, 2, 3, 4, 5} //Использование литерала слайса
	//вывод: [1 2 3 4 5], длина 5, емкость 5

    slice := make([]int, 3) //Использование функции make, 1й аргумент это тип слайса, а 2й - длина слайса.
    //или 
    var slice = make([]int, 3) 
	//вывод: [0 0 0], длина 3, емкость 3
	//Создаст массив из 5 элементов (выделит память и заполнит их нулями), и установит значения len и cap в 3.

    //Можно использовать чуть более расширенную форму — make([]int, len, cap), чтобы указать ёмкость изначально.
    slice := make([]int, 3, 5)
	//вывод: [0 0 0], длина 3, емкость 5
	
    numbers := []int{1, 2, 3, 4, 5} //[1 2 3 4 5]
    slice := numbers[1:3] //Использование среза существующего слайса или массива. Результат: []int{2, 3}
    //Вывод: [2 3]
	
    slice := []int{}
    slice = append(slice, 1, 2, 3) //Использование функции append
    //вывод: [1 2 3]
	
    array := [3]int{1, 2, 3}
    slice := array[:] //Преобразование массива в слайс
	//вывод: [1 2 3]
```

- **Ссылочная семантика:** <br>
При передаче слайса в функцию или присваивании слайса другому слайсу происходит **копирование ссылки** на базовый массив данных, а не самого массива.
Это значит, что изменения внутри слайса будут отражаться ВО ВСЕХ ЕГО КОПИЯХ. 
Например, изменение элемента слайса будет видно в оригинальном слайсе и во всех его копиях.<br>


- **Гибкость:** <br>
Слайсы могут быть созданы из других слайсов, массивов или даже строк. 
Можно использовать оператор среза (slice operator) [low:high] для создания нового слайса, содержащего подмножество элементов исходного слайса.

```go
	numbers := []int{1, 2, 3, 4, 5}

	// Выбираем подмножество элементов слайса
	slice1 := numbers[1:4] // Включает элемент с индексом 1 и исключает элемент с индексом 4
	fmt.Println(slice1)   // Вывод: [2 3 4]

	// Опускаем начальный индекс - выбираем элементы с начала до индекса 2
	slice2 := numbers[:2]
	fmt.Println(slice2)   // Вывод: [1 2]

	// Опускаем конечный индекс - выбираем элементы с индекса 2 до конца
	slice3 := numbers[2:]
	fmt.Println(slice3)   // Вывод: [3 4 5]

	// Используем отрицательные индексы - выбираем последние два элемента
	slice4 := numbers[-2:]
	fmt.Println(slice4)   // Вывод: [4 5]
```

```go
    mas := [5]int{1, 2, 3, 4, 5}
    fmt.Println(mas) //[1 2 3 4 5]
    
    slice := mas[0:3]
    fmt.Println(slice) //[1 2 3]
    
    slice = append(slice, 6)
    fmt.Println(mas) //[1 2 3 6 5]
    fmt.Println(slice) //[1 2 3 6]
```

- **Встроенные функции:** <br>
Язык Go есть встроенные функций для работы со слайсами: append, copy, len и cap. <br>

```go
    //append - для добавления элементов в слайс
    slice := []int{1, 2, 3}
    slice = append(slice, 4, 5)
    fmt.Println(slice) // Вывод: [1 2 3 4 5]
	
	//добавление слайса в конец слайса
    slice1 := []int{1, 2, 3}
    slice2 := []int{4, 5, 6, 7}
    slice1 = append(slice1, slice2...) //результат: [1, 2, 3, 4, 5, 6, 7]

    //Удаление элемента c помощью append
    users := []string{"Bob", "Alice", "Kate", "Sam", "Tom", "Paul", "Mike", "Robert"}
    //удаляем 4-й элемент
    users = append(users[:3], users[3+1:]...) // срезаем слайс до 3 (не включая 3) и добавляем опять срезанный слайс с 4го элемента влючительно
    fmt.Println(users)      //["Bob", "Alice", "Kate", "Tom", "Paul", "Mike", "Robert"]
	
    // copy - для копирования элементов из одного слайса в другой, 
    source := []int{1, 2, 3}
    destination := make([]int, len(source))
    copy(destination, source)
    fmt.Println(destination) // Вывод: [1 2 3]

    // len - для получения текущей длины слайса
    slice := []int{1, 2, 3}
    length := len(slice)
    fmt.Println(length) // Вывод: 3
	
    //cap - для получения его емкости (capacity)
    slice := []int{1, 2, 3}
    capacity := cap(slice)
    fmt.Println(capacity) // Вывод: 3
```
**append()** увеличивает слайс удвоением только до 1024 байт,
а затем начинает использовать другой подход — так называемые "КЛАССЫ РАЗМЕРОВ ПАМЯТИ",
которые гарантируют, что будет выделяться не более ~12.5%.
Выделять 64 байта для массива на 32 байта это нормально, но если слайс размером 4ГБ,
то выделять ещё 4ГБ даже если мы хотим добавить лишь один элемент — это очень дорого.


- **Индексирование и итерация:** <br>
Слайсы могут быть проиндексированы и перебраны с помощью цикла for range. 
```go
    slice := []string{"apple", "banana", "cherry"}

    // Перебор слайса с использованием индексов
    for i := 0; i < len(slice); i++ {
        fmt.Println(slice[i])
    }

    // Перебор слайса с использованием диапазона
    for index, value := range slice {
    fmt.Println(index, value)
    }

    // Перебор слайса с использованием только значений
    for _, value := range slice {
    fmt.Println(value)
    }
	
    // Перебор слайса байтов 
    str := "Hello, world!"
    for index, value := range []byte(str) {
    fmt.Println(index, value)
    }
```

### Gotchas (подводные камни) срезов:

1) **Ссылочная семантика:** <br>
При присваивании слайса другому слайсу или передаче слайса в функцию, КОПИРУЕТСЯ ТОЛЬКО ССЫЛКА на базовый массив данных, а не сам массив. 
Т.е. изменения в одном слайсе отразятся на других, так как они ссылаются на один и тот же базовый массив. 
Это может привести к неожиданным побочным эффектам и ошибкам, особенно при работе с горутинами или функциями, которые могут изменять слайс.

2) **Мутация и перераспределение:** <br>
При добавлении элементов в слайс с помощью функции append может происходить перераспределение памяти, если текущая емкость слайса не достаточна. 
В результате все ссылки на предыдущий слайс станут недействительными, и вам будет возвращен новый слайс с обновленными значениями. 
Это означает, что вы должны быть осторожны при хранении ссылок на элементы слайса после вызова append, чтобы избежать использования недействительных ссылок.
```go
    a := make([]int, 32)
    a = append(a, 1)
```

<img src="image/append.png"  width="30%">

Что будет если, мы сделаем подслайс b, затем увеличим слайс a, подразумевая, что они используют один и тот же массив?
```go
    a := make([]int, 32)     //[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
    b := a[1:16]             //[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
    a = append(a, 1)         //[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1]
    a[2] = 42
    fmt.Println(a)           //[0 0 42 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1]
    fmt.Println(b) 			 //[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```
Так, мы получим два различных массива, и два слайса будут указывать на совершенно разные участки памяти!

<img src="image/append2.png"  width="30%">

3) **Нулевое значение слайса:** <br>
Если слайс не был явно инициализирован, то его значение по умолчанию будет nil. 
При попытке обратиться к элементам нулевого слайса или изменить его, возникнет паника. 
Поэтому перед использованием слайса всегда убедитесь, что он был правильно инициализирован с помощью функции make или присвоения из другого слайса.

4) **Операции индексации:** <br>
При индексации слайса обратите внимание на правильность использования индексов. 
Выход за пределы диапазона слайса вызовет панику. Убедитесь, что индекс находится в диапазоне от 0 до len(slice)-1.

6) **Сравнение слайсов:** <br>
В Go нельзя напрямую сравнивать слайсы с помощью операторов == или !=. 
Сравнение слайсов можно выполнить, перебрав их элементы и сравнивая каждый элемент отдельно. 
Для упрощения этой задачи вы можете использовать пакет reflect или функцию reflect.DeepEqual (хотя она может иметь некоторые ограничения).
```go
package main

import (
	"fmt"
	"reflect"
)

func main() {
	slice1 := []int{1, 2, 3, 4, 5}
	slice2 := []int{1, 2, 3, 4, 5}

	equal := reflect.DeepEqual(slice1, slice2)

	if equal {
		fmt.Println("Слайсы равны")
	} else {
		fmt.Println("Слайсы не равны")
	}
}
```
или 
```go
package main

import (
	"fmt"
)

func main() {
	slice1 := []int{1, 2, 3, 4, 5}
	slice2 := []int{1, 2, 3, 4, 5}

	// Проверяем длины слайсов
	if len(slice1) != len(slice2) {
		fmt.Println("Слайсы не равны")
		return
	}

	// Проверяем каждый элемент слайсов
	for i := 0; i < len(slice1); i++ {
		if slice1[i] != slice2[i] {
			fmt.Println("Слайсы не равны")
			return
		}
	}

	fmt.Println("Слайсы равны")
}
```

6) **Изменение слайса внутри цикла:** <br>
При изменении слайса внутри цикла for range может произойти неожиданное поведение. 
Итерация for range создает `копию` элемента слайса, а не ссылку на него. 
Поэтому при изменении значения элемента внутри цикла не будет изменяться исходный слайс. 
Если вам нужно изменять элементы слайса в цикле, используйте индексы для доступа к элементам и их изменения. Пример:
```go
	slice := []int{1, 2, 3, 4, 5}

	for i := 0; i < len(slice); i++ {
		// Изменяем элементы слайса
		slice[i] = slice[i] * 2
	}

	fmt.Println(slice) // Выводит [2 4 6 8 10]
```
При изменении длины слайса внутри цикла может измениться итерационное условие или количество итераций. 
Если вы планируете изменять длину слайса внутри цикла, убедитесь, что вы обрабатываете итерационные условия и границы корректно.
```go
    slice := []int{1, 2, 3, 4, 5}

	for i := 0; i < len(slice); i++ {
		if slice[i]%2 == 0 {
			// Удаляем четные числа из слайса
			slice = append(slice[:i], slice[i+1:]...)
			i-- // Уменьшаем индекс, чтобы обработать новый элемент
		}
	}

	fmt.Println(slice) // Выводит [1 3 5]
```

7) **Копирование слайсов:** <br>
При копировании одного слайса в другой с помощью функции copy, важно учитывать, что длины и емкости обоих слайсов могут быть разными. 
Функция copy скопирует только столько элементов, сколько можно поместить в меньший слайс. 
Поэтому при копировании слайсов убедитесь, что их длины соответствуют ожидаемым значениям.

8) **Избегайте использования указателей на слайсы:** <br>
Слайсы уже являются ссылочными типами данных, поэтому в большинстве случаев нет необходимости использовать указатели на слайсы. 
Использование указателей на слайсы может привести к сложностям в управлении памятью и нежелательным побочным эффектам.

9) **Инициализация слайса из массива:** <br>
При инициализации слайса из массива, используйте синтаксис среза ([:]), чтобы создать ссылку на весь массив. 
Например, slice := array[:]. Это позволяет работать со слайсом, не копируя элементы массива.

10) **Сравнение слайсов с nil:** <br>
Важно помнить, что слайс, инициализированный с помощью make, не является nil, даже если его длина равна 0. 
Поэтому для проверки, является ли слайс nil, используйте оператор сравнения с nil, а не сравнение длины слайса.
```go
    slice := make([]int, 0) // Инициализируем слайс с помощью make
	fmt.Println(slice) // Вывод: []

	if slice == nil {
		fmt.Println("Слайс равен nil")
	} else {
		fmt.Println("Слайс не равен nil")
	}
```

11) **Осторожность с передачей слайсов в горутины:** <br>
При передаче слайса в горутину, убедитесь, что слайс не будет изменен другими горутинами в то же время. 
Используйте механизмы синхронизации, такие как мьютексы (mutex), для защиты доступа к слайсу и избегайте гонок данных.

12) **Замкнутые слайсы:** <br>
Если слайс объявлен внутри замыкания (closure), убедитесь, что он не захватывает неправильную итерацию переменных. 
Обычно лучше объявлять и инициализировать слайс перед замыканием, чтобы избежать неожиданного поведения.

13) **Распространение ссылок на подсрезы:** <br>
При создании подсреза (среза слайса) из исходного слайса, будьте осторожны с распространением ссылок на подсрезы. 
Если подсрез сохраняется и используется дольше, чем исходный слайс, это может привести к удержанию памяти и 
неожиданным результатам при изменении исходного слайса.

15) **Сравнение слайсов с разными типами элементов:** <br>
В Go слайсы с разными типами элементов считаются разными типами слайсов. 
При сравнении двух слайсов с разными типами элементов будет вызвана ошибка компиляции. 
Убедитесь, что сравниваемые слайсы имеют один и тот же тип элементов.

16) **Использование слайса в качестве ключа в map:** <br>
Слайсы в Go не являются сравнимыми типами, поэтому нельзя использовать их в качестве ключей в map. 
Если вам нужно использовать слайс в качестве ключа, рассмотрите возможность использования массива ([...]T) вместо слайса. 

16) **Присвоение слайса другому слайсу:** <br>
При присвоении одного слайса другому, копируется только ссылка на базовый массив, а не сам массив и его содержимое. 
Это означает, что изменение элементов одного слайса может затронуть другой. 
Если вам нужно создать независимую копию слайса, используйте функцию copy для создания нового слайса с идентичными значениями. 

17) **Многомерные слайсы:** <br>
Многомерные слайсы в Go являются слайсами слайсов. 
При создании и использовании многомерных слайсов учтите, что каждый внутренний слайс может иметь разную длину. 
Убедитесь, что вы правильно инициализируете и работаете с каждым слайсом в многомерном слайсе.

### Что произойдет в случае когда в slice добавим еще один элемент (при помощи функции append)? А если слайс (len: 10, cap: 10)?
1) Если в слайсе есть свободное место (т.е. len < cap), 
append просто добавит новый элемент в слайс на следующую позицию после последнего элемента и увеличит его длину (len) на 1. 
Емкость (cap) слайса останется неизменной. 
2) Если в слайсе нет свободного места (т.е. len == cap), 
append создаст новый слайс с увеличенной емкостью и скопирует все существующие элементы в новый слайс, а затем добавит новый элемент. 
При этом длина (len) нового слайса будет равна старой длине плюс 1, а емкость (cap) будет больше предыдущей емкости. 
Конкретное увеличение емкости зависит от реализации и стратегии увеличения, 
но обычно емкость увеличивается в два раза или по алгоритму, основанному на заранее определенных правилах.

Важно: после вызова append оригинальный слайс остается неизменным, и новый слайс возвращается функцией append. 
Поэтому, если вы хотите сохранить новый слайс для последующего использования нужно присвоить переменной возвращаемое значение append.

### Как "под капотом" go выглядит слайс?

Срез — это легковесная структура данных, которая предоставляет доступ к подпоследовательности элементов массива 
(или, возможно, ко всем элементам), известного как базовый массив.

Слайс - это структура из трёх полей: указателя, длины и емкости.
```go
type slice struct {
        array unsafe.Pointer // Указатель на базовый массив
        len   int // Длина слайса (количество элементов) 
        cap   int // Емкость слайса (длина базового массива)
```

**Указатель на массив (Pointer):** <br>
Слайс содержит указатель на базовый массив, в котором хранятся его элементы. Этот указатель указывает на первый элемент слайса 
(который не обязательно совпадает с первым элементом массива).

**Длина (Length): <br>**
Слайс хранит информацию о текущей длине (количестве элементов) слайса. Длина определяет, сколько элементов из базового массива отображаются слайсом.
Она НЕ может превышать емкость, которая, как правило, представляет собой количество элементов между началом среза и концом базового массива.

**Емкость (Capacity): <br>**
Слайс также хранит информацию о своей емкости (capacity), которая определяет максимальное количество элементов, 
которое слайс может содержать без изменения размера базового массива. Емкость обычно больше или равна длине слайса.

Несколько срезов могут совместно использовать один и тот же базовый массив и относиться к `перекрывающимся частям` этого массива:

<img src="image/slice1.png"  width="40%" height="15%"> <br>

Если на основе массива создать слай меньшей длины и добавить к этому слайсу значение с помощью функции append, то значение в массиве перезапишется: 

<img src="image/slice2.png"  width="33%" height="15%">
<img src="image/slice3.png"  width="30%" height="15%">

https://youtu.be/10LW7NROfOQ

```go
    myArray := [13]string{"", "a", "b", "c", "f", "f", "@", "#", "z", "x", "yz", "cc", "..."}
	fmt.Println(myArray) // [ a b c f f @ # z x yz cc ...]

	slice1 := myArray[4:7]
	fmt.Println(slice1) //[f f @]

	slice1 = append(slice1, "Q") 
	fmt.Println(slice1) //[f f @ Q]
	fmt.Println(myArray) //[ a b c f f @ Q z x yz cc ...]
```

Здесь slice1 - это слайс, который получается прямо из базового массива myArray с помощью среза. Такой слайс называется срезом массива. 
Он содержит ссылку на базовый массив myArray и предоставляет доступ к элементам, начиная с индекса 4 и до индекса 6 (не включая его). 
В этом случае, изменение элементов в slice1 также отразится на myArray, и наоборот.


Базовый массив не будет изменяться, если слайс создать так:
```go
    myArray := [13]string{"", "a", "b", "c", "f", "f", "@", "#", "z", "x", "yz", "cc", "..."}
	fmt.Println(myArray) // [ a b c f f @ # z x yz cc ...]

	slice1 := make([]string, 3, 6)
	copy(slice1, myArray[4:7])
	fmt.Println(slice1) //[f f @]

	slice1 = append(slice1, "Q")
	fmt.Println(slice1) //[f f @ Q]
	fmt.Println(myArray) // [ a b c f f @ # z x yz cc ...]
```
Здесь мы используем функцию make для создания слайса slice1, который не имеет прямой связи с базовым массивом myArray. 
Вместо этого мы копируем элементы из myArray[4:7] в slice1 с помощью функции copy(). 
Таким образом, slice1 будет содержать копии элементов из myArray, и изменение элементов в slice1 не повлияет на myArray.

При использовании функции make для создания слайса, Go выделяет память для слайса и создает отдельный блок памяти для хранения элементов слайса.
Это означает, что слайс slice1 будет содержать указатель на блок памяти, в котором будут храниться скопированные элементы из myArray[4:7], 
а не указатель на сам базовый массив myArray. Это позволяет слайсу slice1 быть независимым от базового массива и изменяться независимо от него.

### Аллокация памяти для слайса

Аллокация памяти - это процесс выделения свободного блока оперативной памяти для использования программой или системой. 
Когда программа требует определенное количество памяти для хранения данных, 
операционная система резервирует соответствующий блок памяти и предоставляет программе доступ к нему.

Функция `append` может приводить к аллокации памяти внутри слайса в случае, если слайс не имеет достаточной емкости для хранения новых элементов. 

При вызове функции append для добавления элементов в слайс, Go проверяет, достаточно ли места в емкости (capacity) слайса для хранения новых элементов. 
Если есть достаточное количество свободного места, то новые элементы добавляются в существующий блок памяти слайса.
Eсли емкость слайса недостаточна для хранения новых элементов, происходит аллокация нового блока памяти большего размера. 
Новый блок памяти выделяется с большей емкостью, чем предыдущий. 
Затем все существующие элементы слайса и новые элементы копируются в новый блок памяти, и слайс начинает указывать на новый блок памяти.

Процесс аллокации памяти при использовании append может быть неявным для программиста, поскольку Go самостоятельно управляет аллокацией и освобождением памяти. 
При этом программисту не требуется явно освобождать память после использования слайса или вызывать специальные функции для управления аллокацией. 
Go имеет встроенный сборщик мусора, который автоматически освобождает память, когда слайс становится недостижимым или больше не используется.

### Сортировка среза
```go
    s := []string{"hello", "cruel", "world"}
	sort.Strings(s) // [cruel hello world]

	s := []int{3, 2, 1}  
	sort.Ints(s) //[1 2 3]
```

### Полезные ссылки: 
https://go.dev/blog/slices-intro
https://github.com/golang/go/wiki/SliceTricks