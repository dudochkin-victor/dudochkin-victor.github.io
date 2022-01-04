+++
title = "Глубокое погружение в Rust для разработчиков на Node.js"
description = "Глубокое погружение в Rust для разработчиков на Node.js"
weight = 1
+++

[Перевод](https://itnext.io/deep-dive-into-rust-for-node-js-developers-5faace6dc71f)

С появлением WebAssembly (главная) сейчас самое подходящее время для изучения Rust поверх имеющихся у вас знаний JavaScript и Node.js для высокопроизводительных вычислений в браузере, на сервере и на периферии.

Добавление Rust в ваш технический стек поверх Node.js - это удачный выбор, поскольку Rust обеспечивает расширенную поддержку WebAssembly, а двоичный формат WebAssembly запускается в Node.js.

После этого глубокого погружения вы начнете работать с Rust. К концу этой статьи вы будете достаточно знакомы с Rust, чтобы начинать свои собственные проекты и создавать более сложное программное обеспечение!

> Примечания: как разработчик Node.js, имеющий в основном опыт работы с JavaScript, вы увидите незнакомые обозначения, такие как «std::wx::yz» или «&xyz», не беспокойтесь, я все объясню.

Rust - это язык более низкого уровня по сравнению с JavaScript и Node.js. Это означает, что вы познакомитесь с тем, как компьютерные программы работают с оборудованием, чтобы действительно понять, что происходит. Node.js гораздо более высокоуровневый, поэтому вы обычно не имеете дело с этими понятиями.

Помните, что Rust изначально был языком системного программирования, который настолько близок к железу / оборудованию, что означает, что абстракции, предоставляемые языком, ближе к реальным физическим компонентам машины. Это дает вам больше возможностей для высокопроизводительного программирования, но также подразумевает большую сложность, чем при программировании с помощью JavaScript.

Rust не будет скрывать такие детали, как значение переменной, хранящейся в стеке или куче, и в зависимости от того, что разрешено, а что нет. Но, как и в случае с Node.js, существует множество библиотек / модулей, которые упростят вашу жизнь.

Проблема со многими учебниками в том, что они созданы людьми, которые хорошо разбираются в компилируемых языках, таких как C и C++ в Linux. Они предполагают, что вы уже знаете, как компьютеры работают внутри (распределение памяти и т. Д.), Что не относится к многим веб-разработчикам (даже экспертам в своей области).

В этой статье я стараюсь изложить ее просто и придерживаться точки зрения человека, который в основном знает JavaScript и Node.js.

Поначалу Rust - сложный язык, так что не торопитесь, расслабьтесь и возвращайтесь, чтобы узнать больше.

__В целом, изучение Rust сделает вас лучшим разработчиком Node.js/JavaScript!__

## Создание проекта на Rust

Вы можете запустить весь код из этой статьи онлайн (за исключением локальных вещей, таких как доступ к файлам, конечно) с игровой площадкой Rust.
После установки Rust создайте новый проект с Cargo (менеджер пакетов Rust): 

```
cargo new <PROJECT_NAME>
```

Это создаст новую папку в вашем текущем каталоге.
В качестве альтернативы, чтобы сделать текущий каталог папкой проекта: 

```
cargo init
```

Источник находится в папке src /. Конечно, точкой входа является файл main.rs с его основной функцией (ключевое слово fn). 

```rust
fn main() {
    println!("Hello, world!");
}
```

## Печать

В Rust вы используете так называемый макрос для вывода на консоль. Макросы в Rust имеют идентификатор, за которым следует восклицательный знак (!). Печать! макрос очень гибкий: 

```rust
fn main() {
   // string interpolation
   println!("Adding {} and {} gives {}", 22, 33, 22 + 33);
   // positional arguments
   println!(
        "Ypur name is {0}. Welcome to {1}. Nice to meet you {0}",
        "Goto", "Rust"
   );
   // named arguments
   println!(
        "{language} is very popular. It was created in {year}",
        language = "Rust",
        year = 2010
   );
  // placeholder traits (using positional argument to avoid repeat)
  println!("{0}, in binary: {0:b}, in hexadecimal: {0:x}", 11);
  // debug trait (very useful to print anything)
  // if you try to print the array directly, you will get an error
  // because an array is not a string or number type
  println!("{:?}", [11, 22, 33]);
}
```

Чтобы увидеть результат, запустите: 

```
cargo run
```

вы увидите (вместе с информацией о компиляции кода - Rust - это компилируемый язык): 

```
Adding 22 and 33 gives 55
Ypur name is Goto. Welcome to Rust. Nice to meet you Goto
Rust is very popular. It was created in 2010
Decimal: 11      Binary: 1011    Hexadecimal: b
[11, 22, 33]
```

В Rust вы должны использовать точку с запятой (;) в конце строки, если только это не последняя строка функции, которая что-то возвращает (подробнее об этом позже).

## Расширенная печать с числовым форматированием 

```rust
fn main() {
    let x = 246.92385;
    let y = 24.69;
    let z = x / y;
    
    // print line macro with 3 decimal point precision
    
    println!("z is {:.3}", z);
    
    // 9: total character space the number to occupy 
    // (adds pre padding if necessary)
    
    println!("z is {:9.3}", z);
    
    // 0: placeholder number for padding characters
    
    println!("z is {:09.3}", z);
    println!("z is {:09.3}\nx is {}", z, x);
    
    // print macro without new line
    
    print!("y is {:09.3}\n x is {}\n", y, x);
    // positional parameters
    
    println!("z is {0:05.1} and x is {1:.2}. \nx is also {1}", z, x)
}
```

Вывод

```
z is 10.001
z is    10.001
z is 00010.001
z is 00010.001
x is 246.92385
y is 00024.690
 x is 246.92385
z is 010.0 and x is 246.92. 
x is also 246.92385
```

## Переменные 

```rust
fn main() {
    // variables are immutable by default
    // stored on the heap (more on that later)
    let pc = "Inspirion XYZ";
    println!("pc is {}", pc);
    // mutable variables
    let mut age = 1;
    println!("age is {}", age);
    age = 2;
    println!("age is {}", age);
    // constants (must be uppercase and explicit type definition)
    const BRAND: &str = "Dell";
    println!("brand is {}", BRAND);
    // multiple assignment (tuple destructuring)
    // more on tuples later in the article
    let (status, code) = ("OK", 200);
    println!("status: {}, code: {}", status, code);
}
```

Вывод

```
pc is Inspirion XYZ
age is 1
age is 2
brand is Dell
status: OK, code: 200
```

## Основные типы 

```rust
fn main() {
    // default integer numeric type is i32
    let num1 = 123;
    println!("{} - type: {}", num1, get_type(&num1));
    // default floating point numeric type is f64
    let num2 = 1.23;
    println!("{} - type: {}", num2, get_type(&num2));
    // explicit typing
    let num3: i8 = 23;
    println!("{} - type: {}", num3, get_type(&num3));
    // max values
    // std is the standard library/crate, 
    // it gives access to a rich variety of features, 
    // here we use the type modules (i32, i16, etc.) and properties
    let max_i32 = i32::MAX;
    let max_i16 = i16::MAX;
    println!("max value for i32 is {}", max_i32);
    println!("max value for i16 is {}", max_i16);
    // boolean
    let is_rust_fun: bool = true;
    println!(
        "is_rust_fun is {} - type: {}",
        is_rust_fun,
        get_type(&is_rust_fun)
    );
    let is_greater = 23 > 5;
    println!(
        "is_greater is {} - type: {}",
        is_greater,
        get_type(&is_greater)
    );
    // characters (unicode - up to 4 bytes length)
    let smiley = '😈';
    println!("smiley is {} - type: {}", smiley, get_type(&smiley));
}
// helper function to print types
fn get_type<T>(_: &T) -> &str {
    std::any::type_name::<T>()
}
```

Вывод

```
123 - type: i32
1.23 - type: f64
23 - type: i8
max value for i32 is 2147483647
max value for i16 is 32767
is_rust_fun is true - type: bool
is_greater is true - type: bool
smiley is 😈 - type: char
```

## Числа с плавающей запятой

- f32 (32-битное число с плавающей запятой)
- f64 (число с плавающей запятой длиной 64 бита) 

```rust
fn main() {
    // by default fractional values stored in f64
    
    let my_float = 12.345677890123456789012345;
    println!("my_float is: {}", my_float);
    let a_float: f32 = 9.9438535983578493758;
    println!("a_float is: {}", a_float);
    let min_f32 = f32::MIN;
    println!("min_f32 is: {}\n", min_f32);
    let max_f32 = f32::MAX;
    println!("max_f32 is: {}\n", max_f32);
    let min_f64 = f64::MIN;
    println!("min_f64 is: {}\n", min_f64);
    let max_f64 = f64::MAX;
    println!("max_f64 is: {}\n", max_f64);
}
```
Вывод

```rust
my_float is: 12.345677890123456
a_float is: 9.943853
min_f32 is: -340282350000000000000000000000000000000
max_f32 is: 340282350000000000000000000000000000000
min_f64 is: -179769313486231570000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
max_f64 is: 179769313486231570000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
```

## Побитовые операции (расширенные - можно пропустить) 

```rust
/*
Bitwise operations: on individual bits rather than sets of bytes.
- binary representation, a sequence of bytes
- underscore separator allowed for legibility
- by default binary representations are store as i32
*/
fn main() {
    // stored as u8 by adding suffix u8
    
    let mut value = 0b1111_0101u8;
    // will print base 10 (decimal) representation
    
    println!("value is {}", value);
    /*
    :08b
        0 -> display leading zeros
        8 -> number of bits to display
        b -> display binary representation
    */
    println!("value is {:08b}", value);
    // bitwise NOT: invert individual bits
    
    value = !value; // 0000_1010
    println!("value is {:08b}", value);
    // bitwise AND: used to clear the value of a specific bit
    value = value & 0b1111_0111; // -> 0000_0010
    println!("value is {:08b}", value);
    // bitwise AND: used to check value of a specific bit
    // if a specific bit is 0 or 1, useful to check status of registers for process state
    
    println!("value is {:08b}", value & 0b0100_0000); 
    // -> 0000_0000
    // bitwise OR: if either operand is 1, result is 1
    // useful to set value of a specific bit
    
    value = value | 0b0100_0000; // -> 0100_0010
    println!("value is {:08b}", value);
   // bitwise XOR (exclusive OR): 
   // result is 1 only when bits are different, otherwise 0
   // useful to set if bits are different
    
   value = value ^ 0b0101_0101; // -> 0001_0111
   println!("value is {:08b}", value);
   ////////////////////////////
   // Bit Shift operators
   ////////////////////////////
   // shift bit pattern left or right by a number of bits
   // and backfill shifted bit spaces with zeros
   // shift left by 4 bits
    
   value = value << 4; // -> 0111_0000
   println!("value is {:08b}", value);
   // shift right by 3 bits
    
   value = value >> 3; // -> 0000_1110
   println!("value is {:08b}", value);
}
```

Вывод

```
value is 245
value is 11110101
value is 00001010
value is 00000010
value is 00000000
value is 01000010
value is 00010111
value is 01110000
value is 00001110
```

## Логические значения и двоичная алгебра 

```rust
fn main() {
    let a = true;
    let b = false;
    println!("a is {}\nb is {}", a, b);
    println!("NOT a is {}", !a);
    println!("a AND b is {}", a & b);
    println!("a OR b is {}", a | b);
    println!("a XOR b is {}", a ^ b);
    // boolean casted to integer begets 0 or 1
    println!("a XOR b is {}", (a ^ b) as i32); // 1
   let c = (a ^ b) | (a & b);
    println!("c is {}", c);
    // short-circuiting logical operations:
    // right operand not evaluated
    let d = true || (a & b);
    println!("d is {}", d);
    // the panic macro is not evaluated, 
    // so the process ends with status 0 (OK, no error)
    // panics exit the program immediately (like throwing error in Node.js)
    let e = false && panic!();
    println!("e is {}", e);
}
```

Вывод

```
a is true
b is false
NOT a is false
a AND b is false
a OR b is true
a XOR b is true
a XOR b is 1
c is true
d is true
e is false
```

## Арифметические операции 

```rust
fn main() {
    // can only do arithmetic operations on same type operands
    let a = 11;
    let b = 33;
    let c = a + b;
    println!("c is {}", c);
    let d = c - b;
    println!("d is {}", d);
    let e = a * d;
    println!("e is {}", e);
    // type casting (careful with precision loss and type compatibility)
    let f = c as f32 / 4.5;
    println!("f is {}", f);
    // operator precedence control
    
    let g = 43.5432 % (a as f64 * e as f64);
    println!("g is {}", g);
}
```

Вывод

```
c is 44
d is 11
e is 121
f is 9.777778
g is 43.5432
```

## Операторы сравнения 

```rust
/*
can only compare values of same type
*/
fn main() {
    let a = 11;
    let b = 88;
    println!("a is {}\nb is {}", a, b);
    println!("a EQUAL TO b is {}", a == b);
    println!("a NOT EQUAL TO b is {}", a != b);
    println!("a GREATER THAN b is {}", a > b);
    println!("a GREATER THAN OR EQUAL TO b is {}", a >= b);
    println!("a LESS THAN b is {}", a < b);
    println!("a LESS THAN OR EQUAL TO b is {}", a <= b);
    let c = true;
    let d = false;
    println!("\nc is {}\nd is {}", c, d);
    println!("c EQUAL TO d is {}", c == d);
    println!("c NOT EQUAL TO d is {}", c != d);
    println!("c GREATER THAN d is {}", c > d);
    println!("c GREATER THAN OR EQUAL TO d is {}", c >= d);
    println!("c LESS THAN d is {}", c < d);
    println!("c LESS THAN OR EQUAL TO d is {}", c <= d);
}
```

Вывод

```
a is 11
b is 88
a EQUAL TO b is false
a NOT EQUAL TO b is true
a GREATER THAN b is false
a GREATER THAN OR EQUAL TO b is false
a LESS THAN b is true
a LESS THAN OR EQUAL TO b is true
c is true
d is false
c EQUAL TO d is false
c NOT EQUAL TO d is true
c GREATER THAN d is true
c GREATER THAN OR EQUAL TO d is true
c LESS THAN d is false
c LESS THAN OR EQUAL TO d is false
```

## Символы 

```rust
fn main() {
    // Unicode scalar value stored using 4 bytes (32 bits)
    // contrary to C like languages that store it in 1 byte
    let letter: char = 'z';
    let number_char = '9';
    let finger = '\u{261D}';
    println!("letter is {}", letter);
    println!("number_char is {}", number_char);
    println!("finger is {}", finger);
}
```

Вывод

```
letter is z
number_char is 9
finger is ☝
```

## Среднее значение 

```rust
fn main() {
    let a = 33;
    let b = 4.9;
    let c: f32 = 123.5;
    let average = (a as f32 + b as f32 + c) / 3.0;
    println!("average is {}", average);
    assert_eq!(average, 53.8);
    println!("test passed.");
}
```

Вывод

```
average is 53.8
test passed.
```

## Массивы 

```rust
fn main() {
    // fixed length and single typed
    // stored in contiguous memory locations
    // Contiguous means that elements are laid out so that every element is the same distance from its neighbors.
    let letters = ['a', 'b', 'c']; // type: [char; 3]
    let first_letter = letters[0];
    println!("first_letter is {}", first_letter);
    // to modify elements in array, it must be mutable
    let mut numbers = [11, 22, 44]; // type is [i32; 3]
    numbers[2] = 33;
    println!("numbers is {}", numbers[2]);
    // empty array declaration (memory allocated)
    let words: [&str; 2];
    words = ["ok"; 2]; // repeat expression, equivalent to ["ok", "ok"]
    println!("words is {:?}", words);
    /*
    length of usize is based on number of bytes needed to reference memory in your target architecture:
    - for 32 bit compilation target -> usize is 4 bytes
    - for 64 bit compilation target -> usize is 8 bytes
    */
    let ints = [22; 5];
    let length: usize = ints.len();
    println!("length is {}", length);
    // get size in memory (mem module of the std crate)
    let mem_size_byte = std::mem::size_of_val(&ints);
    println!("mem_size_byte is {}", mem_size_byte);
    // get slice from array
    let mut slice: &[i32] = &ints;
    println!("slice is {:?}", slice);
    
    slice = &ints[3..5];
    println!("slice is {:?}", slice);
}
```

Вывод

```
first_letter is a
numbers is 33
words is ["ok", "ok"]
length is 5
mem_size_byte is 20
slice is [22, 22, 22, 22, 22]
slice is [22, 22]
```

## Срезы 

```rust
fn main() {
    // slice: used to reference a contiguous section (subset) of a collection (aarray, etc.) WITHOUT taking ownership of these elements
    // commonly used: string slice dt type : &str
    // string literals are slices
    // the data is hard-coded in the executable binary and the program  uses a string slice to access it
   // create a string slice from string data
    // the sentence variable has a pointer to the beginning of the string data in memory as well, as info about its length and capacity
    let sentence = String::from("This is a sequence of words.");
    println!("sentence is {}", sentence);
    // the last_word variable has a pointer to the offset/start index of the section of the string data, as well as the length of the slice
    // as usual, the end index of the slice is excluded from the result (common in most programming languages when slicing collections)
    let last_word = &sentence[22..22 + 5];          // [start..end_excluded]
    println!("last_word is \"{}\"", last_word);
    // slice from offset index to end of the collection (here string data)
    let last_part: &str = &sentence[22..];
    println!("last_part is \"{}\"", last_part);
    // slice from beginning of collection up until end index
    let without_last_word = &sentence[..22];
    println!("without_last_word is \"{}\"", without_last_word);
    // the length of a string slice is in number of bytes (usize data type)
    // NOT in number of characters
    let slice_length: usize = last_part.len();
    println!("slice_length is {} bytes", slice_length);
    // when creating a string slice, the range indices must occur at valid UTF-8 character boundaries
    // remember that UTF-8 characters can occupy multiple bytes
    // all this to say that slice range indices must be character boundaries
    // if you index in the middle of a character, the program will panic
    // so be careful when creating string slices from strings with special characters or emojis
   // yes, this is some low-level stuff that we usually don't deal with in everyday Node.js
}
```

Вывод

```
sentence is This is a sequence of words.
last_word is "words"
last_part is "words."
without_last_word is "This is a sequence of "
slice_length is 6 bytes
```

## Срезы как параметры 

```rust
fn main() {
    let message = String::from("lorem ipsum");
    let first_word = get_first_word(&message);
    println!("first_word is \"{}\"", first_word);
    let first_word_too = get_first_word_too(&message[6..]);
    println!("first_word_too is \"{}\"", first_word_too);
}
// ======= passing the entire string as input ===========
fn get_first_word(msg: &String) -> &str {
    // create a slice of bytes (&[u8] data type) from string data
    let bytes: &[u8] = msg.as_bytes();
    // iterate through byte sequence one byte at a time
    // use enumerate() to get the index when iterating
   for (index, &item) in bytes.iter().enumerate() {
        // find first space and return everything before as a string slice
        // b' ' is the byte representation of a blank space
        // remember that we are iterating on a sequence of bytes, NOT characters
       // we do that because the index for a string slice is in terms of bytes
       if item == b' ' {
            return &msg[..index];
       }
    }
    // no blank space found, return entire message
    &msg
}
// ======= passing a string slice as input ===========
fn get_first_word_too(msg: &str) -> &str {
    let bytes: &[u8] = msg.as_bytes();
    for (index, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &msg[..index];
        }
    }
    &msg
}
```

Вывод

```
first_word is "lorem"
first_word_too is "ipsum"
```

## Deref принуждение

Примечания:
- передача заимствованной ссылки на строку (&string) не то же самое, что передача фрагмента строки (&str)
- заимствованная ссылка на строку указывает на строку в стеке, которая, в свою очередь, владеет данными в куче и указывает на них
- срез хранит только указатель на данные кучи вместе с информацией о длине. Он не отслеживает емкость, потому что ему ничего не принадлежит в куче.
- поскольку строковая ссылка содержит всю информацию для работы в качестве среза (указатель на данные кучи + длина), Rust позволяет использовать строковую ссылку там, где ожидается строковый срез
- это удобство называется принуждением deref: 

```rust
fn main() {
    let message = String::from("lorem ipsum");
   
    // notice that a string reference is passed as argument
    let first_word = get_first_word(&message); 
    println!("first_word is \"{}\"", first_word);
}
// notice that the expected argument is of type string slice (&str)
fn get_first_word(msg: &str) -> &str {
    let bytes: &[u8] = msg.as_bytes();
    for (index, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &msg[..index];
        }
    }
    &msg
}
```

- конечно, принуждение deref не работает при использовании строкового среза, где ожидается строковая ссылка (из-за отсутствующих свойств)
- при написании кода предпочитайте использовать строковые срезы в случаях, когда владение данными не требуется

## Многомерные массивы 

```rust
fn main() {
    let d2: [[i32; 3]; 3] = [[9, 8, 7], [6, 5, 4], [3, 2, 1]];
    let value = d2[1][0];
    println!("value is {}", value);
    // mutating a tuple
    let d3: [[[&str; 100]; 20]; 5];
    d3 = [[["ok"; 100]; 20]; 5];
    println!("value d3[3][11][35] is {}", d3[3][11][35])
}
```

Вывод
```

value is 6
value d3[3][11][35] is ok
```

## Векторы 

```rust
fn main() {
    // collection of elements with the same data type
    // elements are sorted in order
    // arrays have a fixed size that must be known at compile time
    // because array data is stored on the stack
    // vectors can dynamically grow and shrink
    // by adding / removing items
    // vector data is stored in heap memory
    // therefore you need to handle ownership and borrowing
    // vectors = mutable size arrays
    let mut letters: Vec<char> = vec!['a', 'b', 'c'];
    println!("letters are {:?}", letters);
    let first_letter = letters[0];
    println!("first_letter is {}", first_letter);
    // add value to vector
    letters.push('d');
    letters.push('e');
    letters.push('f');
    println!("letters are {:?}", letters);
    // remove last value
    letters.pop();
    println!("letters are {:?}", letters);
    let mut numbers: Vec<i32> = vec![11, 22, 44];
    numbers[2] = 33;
    println!("numbers is {}", numbers[2]);
    let words: Vec<&str>;
    words = vec!["ok"; 2];
    println!("words are {:?}", words);
    let mut ints = vec![22, 33, 44, 55, 66, 77];
    let length: usize = ints.len();
    println!("length is {}", length);
    let mem_size_byte = std::mem::size_of_val(&ints);
    println!("mem_size_byte is {}", mem_size_byte);
    // slice from vector
    let mut slice: &[i32] = &ints;
    println!("slice is {:?}", slice);
    slice = &ints[2..5];
    println!("slice is {:?}", slice);
    // iterate over vector
    for it in ints.iter() {
        println!("it is {}", it);
    }
    // mutate vector items while iterating
    for it in ints.iter_mut() {
        // dereference the pointer to get and set value (*it)
        *it *= *it;
    }
    println!("ints is {:?}", ints);
}
```

Вывод

```
letters are ['a', 'b', 'c']
first_letter is a
letters are ['a', 'b', 'c', 'd', 'e', 'f']
letters are ['a', 'b', 'c', 'd', 'e']
numbers is 33
words is ["ok", "ok"]
length is 6
mem_size_byte is 24
slice is [22, 33, 44, 55, 66, 77]
slice is [44, 55, 66]
it is 22
it is 33
it is 44
it is 55
it is 66
it is 77
ints is [484, 1089, 1936, 3025, 4356, 5929]
```

## Кортежи 

```rust
fn main() {
    // used to group related items of mixed data types
    // can have max 12 mixed type values 
    // adding more values and it will no longer be a tuple type
    let a_tuple: (&str, u8, char) = ("ok", 0, 'd');
    let first_item = a_tuple.0;
    println!("first_item is {}", first_item);
    // mutate a tuple
    let mut b_tuple = ("ok", 0);
    b_tuple.0 = "ko";
    b_tuple.1 += 1;
    println!("b_tuple.1 is {}", b_tuple.1);
    // destructure a tuple
    let c_tuple = ("en", "US", 1);
    let (language, country, code) = c_tuple;
    println!(
        "language is: {}\ncountry is: {}\ncode is: {}",
        language, country, code
    )
}
```
Вывод
```
first_item is ok
b_tuple.1 is 1
language is: en
country is: US
code is: 1
```

## Функции 

```rust
fn main() {
    be_polite();
    // inferred types for y and z are the ones used as parameters of add()
    // to be clear, if you do not declare a specific type for variables, these variables will assume the type of the arguments of the function where first used
   // remember, by the default inferred type is i32 for integers
    let y = 12;
    let z = 34;
    // now y and z are considered u8 type because this is how they are first used as function arguments
    add(y, z);
    // passing later y and z to another fn with different param types will panic
    // guess_number(z) // -> expects a i32 not a u8
    // need for explicit cast:
    guess_number(y as i32)
}
fn be_polite() {
    println!("Greetings, pleased to meet you.");
    guess_number(25)
}
fn guess_number(number: i32) {
    println!("Indeed, {} is the correct answer", number)
}
fn add(a: u8, b: u8) {
    let sum = a + b;
    println!("sum is {}", sum)
}
```
Вывод
```
Greetings, pleased to meet you.
Indeed, 25 is the correct answer
sum is 46
Indeed, 12 is the correct answer
```

## Утверждения и выражения 

```rust
fn main() {
    // Statement performs an action without returning a value
    // statements end with a semicolon: a = 6;
    // an expression evaluates to a resulting value
    // expressions do NOT end with a semicolon: 3 + 4 which evaluates to 7
    // adding a semicolon to an expressions transforms it into an statement
    // expressions are used as parts of statements: let total = r + c;\n\t{}\n\t{}",
    // where "r + c" is an expression and "let total = r + c;" is a statement
    println!("expression 4 + 5 evaluates to: {}", 4 + 5);
}
```
Вывод
```
expression 4 + 5 evaluates to: 9
```

## Тип возвращаемого значения функции 

```rust
fn main() {
    let result = square(3);
    println!("result is {}", result);
    let result_tuple = triple(33);
    let (input, result1) = result_tuple;
    println!("result_tuple is {:?}", result_tuple); 
    // {:?} ==> debug formatting
    println!("input {} evaluates to {}", input, result1);
    let nothing:() = does_not_return();
    println!("nothing (union data type) is {:?}", nothing)
}

fn square(number: i32) -> i32 {
    println!("processing square({})", number);
    // expression returning a value
    number * number
    // " return  number * number;" is also valid syntax
}

// multiple returns with tuples
fn triple(number: i32) -> (i32, i32) {
    println!("tripling the number: {}", number);
    let input = number;
    let result = number * 3;
    (input, result)
}

// union data type
// used when no meaningful values returned by a fn
// represented by empty()
// it is optional
fn does_not_return() ->() {
    println!("ain't returning nuthing!")
}
```

Вывод

```
processing square(3)
result is 9
tripling the number: 33
result_tuple is (33, 99)
input 33 evaluates to 99
ain't returning nuthing!
nothing (union data type) is()
```

## Замыкания 

```rust
fn main() {
    // closures are anonymous functions that have access to variables in the enclosing scope
    // long form
    let double = |n1: u8| -> u8 { n1 * 2 };
    // short form
    let triple = |n1| n1 * 3;
    const DAYS_IN_YEAR: u16 = 365;
    // referencing variable from enclosing scope
    let quadruple_than_add_number_days_in_year = |n1: i32| n1 * 4 + (DAYS_IN_YEAR as i32);
    const FACTOR: i32 = 22;
    let multiple_by_22 = |x| FACTOR * x;
    println!("{}", double(11));
    println!("{}", triple(99));
    println!("{}", quadruple_than_add_number_days_in_year(44));
    println!("{}", multiple_by_22(5));
}
```
Вывод
```
22
297
541
110
```

## Конвертер Цельсия в Фаренгейт 

```rust
fn main() {
    let (celsius, farenheit) = to_farenheit(40.0);
    println!("{} celsius is {} farenheit", celsius, farenheit);
    assert_eq!(farenheit, 104.0);
    // will not execute if assertion fails
    println!("test passed");
}
fn to_farenheit(celsius: f32) -> (f32, f32) {
    let farenheit = (1.8 * celsius) + 32.0;
    // return statement (no semicolon)
    (celsius, farenheit)
}
```

## Условное исполнение 

```rust
fn main() {
    let x = 5;
    if x == 5 {
        println!("x is 5");
    }
    // if expressions (equivalent of ternary operator in JS/Node.js)
    let x_odd = if x % 2 == 0 { "odd" } else { "even" };
    println!("x_odd is {}", x_odd);
}
```
Вывод
```
x is 5
x_odd is even
```
## Множественные условные выражения (if / else if) 

```rust
fn main() {
    let x = 2;
    let y = 5;
    if x > y {
        println!("x is greater than  y");
    } else if x < y {
        println!("x is less than y");
    } else {
        println!("x is equal to y");
    }
}
```
Вывод
```
x is less than y
```
## Назначение цикла 

```rust
fn main() {
    let mut count = 0;
    // infinite loop
    loop {
        if count == 10 {
            break;
        }
        count += 1;
        println!("count is {}", count);
    }
    println!("\nAfter first loop.\n");
    // returning a value from loop expression
    let result = loop {
        if count == 15 {
            // returning a value with break statement
            break count * 20;
        }
        count += 1;
        println!("count is {}", count);
    };
    println!("\nAfter second loop, result is {}", result);
}
```
Вывод
```
count is 1
count is 2
count is 3
count is 4
count is 5
count is 6
count is 7
count is 8
count is 9
count is 10
After first loop.
count is 11
count is 12
count is 13
count is 14
count is 15
After second loop, result is 300
```

## Циклы пока 
```rust
fn main() {
    let mut count = 0;
    let letters: [char; 5] = ['a', 'b', 'c', 'd', 'e'];
    while count < letters.len() {
        println!("letter[{}] is {}", count, letters[count]);
        count += 1;
    }
// contrary to loop expressions, the break statement in while loop cannot return a value
}
```
Вывод
```
letter[0] is a
letter[1] is b
letter[2] is c
letter[3] is d
letter[4] is e
```
## Циклы for
```rust
fn main() {
    let message = ['m', 'e', 's', 's', 'a', 'g', 'e'];
    /* Iterator
    - implements logic to iterate over each item in a collection
    - next() method returns the next item in a sequence
      */
    for item in message.iter() {
        println!("current item is {}", item);
    }
    println!("");
    // To also get the indexes when iterating
    // enumerate() returns a tuple with index/item_reference pair
    // To get the item use &item
    // because the iterator gives back a reference (&<NAME>)
    // Adding the & (borrow operator) allows you to 
    // borrow the variable without 
    // taking ownership (see borrowing section) 
    // - then when you use the variable in the for loop scope, you access the value
    for (index, &item) in message.iter().enumerate() {
        println!("item {} is {}", index, item);
        if item == 'e' {
            break;
        }
    }
    println!("");
    // iterating over a range of numbers
    // excludes the end value of the range
    for number in 0..5 {
        println!("number is {}", number);
    }
}
```
Вывод
```
current item is m
current item is e
current item is s
current item is s
current item is a
current item is g
current item is e
item 0 is m
item 1 is e
number is 0
number is 1
number is 2
number is 3
number is 4
```
## Вложенные циклы 
```rust
fn main() {
    let mut matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
    // reading from matrix
    for row in matrix.iter() {
        for number in row.iter() {
            print!("{}\t", number);
        }
        println!("");
    }
    println!("=======================");
    // modifying values from mutable matrix
    // iter_mut() returns mutable references
    for row in matrix.iter_mut() {
        for number in row.iter_mut() {
            // dereference with asterisk to get the value itself
            *number += 20;
            print!("{}\t", number);
        }
        println!("");
    }
}
```
Вывод
```
1 2 3 
4 5 6 
7 8 9 
=======================
21 22 23 
24 25 26 
27 28 29
```
## Угадайку 

```rust
use rand::Rng;
use std::io;
fn main() {
    println!("Guess a number");
    println!("Please enter your guess:");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is {}", secret_number);
    // "::" is used for associated functions of a given type (equiv to static methods in OOP - more on this later)
    // String::new() creates an empty string of type String    (growable UTF-8 encoded text)
    let mut guess = String::new();
    /*
        std::io::stdin, if you don't use the import at the top of file
        std::io::stdin() returns an instance of a std::io::Stdin type
    */
    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");
    println!("You guess: {}", guess);
}
```

## Базовая статистика 
```rust
fn main() {
    let numbers = [1, 9, -2, 0, 23, 20, -7, 13, 37, 20, 56, -18, 20, 3];
    let mut max: i32 = numbers[0];
    let mut min: i32 = numbers[0];
    let mut mean: f64 = 0.0;
    for item in numbers.iter() {
        mean += *item as f64;
        if *item > max {
            max = *item;
        }
        if *item < min {
            min = *item;
        }
    }
    mean /= numbers.len() as f64;
    assert_eq!(max, 56);
    assert_eq!(min, -18);
    assert_eq!(mean, 12.5);
    println!("Test passed!");
}
```
Вывод
```
Test passed!
```

## Oбласть видимости 

```rust
fn main() {
    let planet = "Dunya";
    if true {
        let planet = "Jupiter";
        println!("planet is {}", planet);
    }
    println!("planet is {}", planet);
}
```
Вывод
```
planet is Jupiter
planet is Dunya
```
## Изменчивость переменной 
```rust
fn main() {
    let car = "Mitsubishi";
    println!("car is a {}", car);
    // code block, has its own scope
    {
        // varable shadowing
        let car = 1;
        println!("car is a {}", car);
    }
    println!("car is a {}", car);
}
```
Вывод
```
car is a Mitsubishi
car is a 1
car is a Mitsubishi
```
## Стек и куча памяти 
```rust
fn main() {
    println!("=== STACK ====\n");
    println!("- values stored in sequential order of insertion");
    println!("- data added in LIFO (last in first out)");
    println!("- stores variables - pushing values on the stack");
    println!("- also holds info for function execution");
    println!(
        "- stack have very fast access because no guessing where to put data, it will be on top"
    );
    println!("- stacks are limited in size");
    println!("- all data in stack must have a known fixed size\n");
    func1();
    println!("func1 done");
    println!("pop variable y off the stack");
    println!("pop variable z off the stack\n");
println!("\n\n=== HEAP ====\n");
    println!("- adding data to heap, search for large enough place in memory to store data");
    println!("- marks memory spot as being used (allocating) and put data in it");
    println!("- accessing data in heap is more complex than the stack because the stack allocates anywhere in available memory");
    println!("- slower than stack");
    println!("- dynamically add and remove data");
    println!("\n\n=== POINTER ====\n");
    println!("- data type that stores a memory address");
    println!("- pointers have a fixed size so can be stored on the stack");
    println!("- adding and accessing data on the heap is done through pointers (addresses in memory)");
}
fn func1() {
    println!("func1 executing...");
    let y = 3.11;
    println!("push variable y = {} onto the stack", y);
    let z = 5;
    println!("push variable z = {} onto the stack", z);
    func2();
    println!("func2 done");
    println!("pop variable arr off the stack");
}
fn func2() {
    println!("func2 executing...");
    let arr = [2, 3, 4];
    println!("push variable arr = {:?} onto the stack", arr);
}
```
Вывод
```
=== STACK ====
- values stored in sequential order of insertion
- data added in LIFO (last in first out)
- stores variables - pushing values on the stack
- also holds info for function execution
- stack have very fast access because no guessing where to put data, it will be on top
- stacks are limited in size
- all data in stack must have a known fixed size
func1 executing...
push variable y = 3.11 onto the stack
push variable z = 5 onto the stack
func2 executing...
push variable arr = [2, 3, 4] onto the stack
func2 done
pop variable arr off the stack
func1 done
pop variable y off the stack
pop variable z off the stack
=== HEAP ====
- adding data to heap, search for large enough place in memory to store data
- marks memory spot as being used (allocating) and put data in it
- accessing data in heap is more complex than the stack because the stack allocates anywhere in available memory
- slower than stack
- dynamically add and remove data
=== POINTER ====
- data type that stores a memory address
- pointers have a fixed size so can be stored on the stack
- adding and accessing data on the heap is done through pointers (addresses in memory)
```
## Строки
В Rust есть два типа строковых типов. 
```rust
fn main() {
    // Two types of string representation:
    
    // - string literals: hard coded into the executable.
    // these are immutable and must be known before compilation
    
    // - String type: allocated data on the heap, \n\tmutable and dynamically generated at runtime
    // string literal stored on heap
    // String::from() creates a String type from a string literal
    // the sequence [m,a,r,s] will get stored on the heap
    // to access the string stored on heap, program holds a pointer to it on the stack (message variable)
    // that pointer on the stack includes first char memory address, length of string and the capacity so you know how much memory s allocated for it on the heap
    let mut message = String::from("Jupiter");
    println!("message is {}", message);
    // append string to original
    // if more memory need than capacity, pointer address updated as well as length and capacity to reflect new location in memory
    message.push_str(" is smoke and mirrors");
    println!("message is {}", message);
    // pushing a char
    message.push('!');
    println!("message is {}", message);
   // get length
   println!("message lenght is {}", message.len());
   // get capacity in bytes
   println!("message capacity is {}", message.capacity());
  // check if empty
  println!("Is empty: {}", message.is_empty());
  // substring search
  println!("Contains smoke: {}", message.contains("smoke"));
  // replace substring
  println!("message is {}", message.replace("smoke","gaz"));
  // loop over words in string (split by white space)
  for word in message.split_whitespace() {
    println!("word is {}", word);
  }
  // create string with capacity
  let mut s = String::with_capacity(4); // 4 bytes capacity
  println!("s capacity is  {} bytes", s.capacity());
  // 1 byte consumed 
  // Latin alphabet letters usually have 1 byte size
  // remember Unicode supports 4-byte characters
  s.push('Q');
  s.push('W'); // 1 byte consumed
  s.push_str("er"); // 2 bytes consumed
  // exceeding string capacity (automagically increased and reallocation in memory)
  s.push('T'); // 1 byte consumed
  println!("s capacity is  now {} bytes", s.capacity());
}
```
Вывод
```
message is Jupiter
message is Jupiter is smoke and mirrors
message is Jupiter is smoke and mirrors!
message lenght is 29
message capacity is 56
Is empty: false
Contains smoke: true
message is Jupiter is gaz and mirrors!
word is Jupiter
word is is
word is smoke
word is and
word is mirrors!
s capacity is  4 bytes
s capacity is  now 8 bytes
```

## Право собственности 

```rust
fn main() {
    /* need to clean up allocated memory blocks no longer needed
    in C/C++: malloc() and free() for manual memory mngt
    other approach is garbage collection which is automatic */
    /*
    Rust uses OWNERSHIP ystem:
       - variables are responsible for freeing their own resources
       - every value is owned by only one variable at a time
       - when owning variable goes out of scope the value is dropped
       - there are ways to transfer ownership of a value from one variable to another
    */
    let outer_planet: String;
    let outer_galaxy: String;
    let outer_planet_position: i32;
    // inner code block scope
    {
        let inner_planet = String::from("Mercury");
        println!("inner_planet is {}", inner_planet);
        /*
        because ownership mandates only one owner per value/data,
         - inner_planet will no longer point to the String value on the heap
         - transferring ownership from one variable to another is called a "move" in Rust
         - this means that NO shallow copy of data STORED ON THE HEAP in Rust
            (shallow copy = several variables pointing to same data in memory)
        */
        // transferring ownership
        outer_planet = inner_planet;
        // can no longer use inner_planet variable after the move of ownership of string data
        // println!("inner_planet is {}", inner_planet); // => will panic
        let mut inner_galaxy = String::from("Milky Way");
        println!("inner_galaxy is {}", inner_galaxy);
        // to duplicate data stored on the heap, creates a deep copy of the String data
        
        outer_galaxy = inner_galaxy.clone();
        inner_galaxy.clear();
        println!("inner_galaxy is now: {}", inner_galaxy);
        println!("outer_galaxy is {}", outer_galaxy);
        // integer data types live on the stack
        let mut inner_planet_position = 1;
        println!("inner_planet_position is {}", inner_planet_position);
        /*
        a copy of the integer data is created for the outer_planet_position
        - ownership is respected (no shallow copy - only one variable per value at a time)
        - generally STACK-ONLY data types (ie fixed size) are implicitly copied
            when variable containing them is assigned to another variable
        - data types stored om stack implement the trait that allow them to be copied rather than moved
        */
        outer_planet_position = inner_planet_position;
        inner_planet_position += 4;
        println!("inner_planet_position is {}", inner_planet_position);
        println!("outer_planet_position is {}", outer_planet_position);
    }
    println!("\nouter_planet is {}", outer_planet);
    println!("outer_galaxy is {}", outer_galaxy);
    println!("outer_planet_position is {}", outer_planet_position);
}
```
Вывод
```
inner_planet is Mercury
inner_galaxy is Milky Way
inner_galaxy is now: 
outer_galaxy is Milky Way
inner_planet_position is 1
inner_planet_position is 5
outer_planet_position is 1
```
## Еще примеры: 
```rust
fn main() {
    let mut arr_1: [u8; 2] = [33, 66];
    // ////////////////
    // fixed-length types (stored on the stack) are COPIED
    // ////////////////
    let arr_2 = arr_1;
    println!("arr_1 is {:?}", arr_1);
    arr_1 = [1, 2];
    println!("arr_1 is now {:?}", arr_1);
    println!("arr_2 is {:?}", arr_2);
    // ////////////////
    // mutable-length type values move the ownership to new variable
    // ////////////////
    let vec_1 = vec![3, 4];
    let vec_2 = vec_1;
    // can no longer use the variable which ownership has been "moved"
    // println!("vec_1 is {:?}", vec_1); // => wll panic
    println!("vec_2 is {:?}", vec_2);
    // to borrow value owned by a variable without moving ownership,
    // use a reference to that value
    let vec_4 = vec![5, 6, 7];
    // borrowing value using a reference (&<NAME>)
    let vec_5 = &vec_4;
    println!("vec_4 is {:?}", vec_4);
    println!("vec_5 is {:?}", vec_5);
}
```
Вывод
```
Вывод
arr_1 is [33, 66]
arr_1 is now [1, 2]
arr_2 is [33, 66]
vec_2 is [3, 4]
vec_4 is [5, 6, 7]
vec_5 is [5, 6, 7]
```
## Передача права собственности (данные хранятся в STACK) 
```rust
fn main() {
    let rocket_fuel = 1;
    process_fuel(rocket_fuel);
    println!("rocket_fuel is {}", rocket_fuel);
}
/*
    - because propellant is i32 so lives on the stack, 
    the value passed as argument is COPIED jn fn scope
    - to be able to modify the copy inside the function scope, use the mut keyword
*/
fn process_fuel(mut propellant: i32) {
    // the copy is modified
    propellant += 2;
    println!("Processing propellant {}", propellant);
}
```
Вывод
```
Processing propellant 3
rocket_fuel is 1
```
## Передача права собственности (данные хранятся в HEAP) 
```rust
fn main() {
    let rocket_fuel = String::from("MU-RF");
    process_fuel(rocket_fuel);
    // the ownership of the string has moved to propellant variable,
 
    // the following code will panic because rocket_fuel ownership is done so you can no longuer use the variable
    // println!("rocket_fuel is {}", rocket_fuel);
}
/*
    - because propellant is String so lives on the HEAP
    - data stored on heap when passed as argument to function, 
    - the function local variable gets a pointer to the data passed 
    - and therefore the local function variable gets ownership of the data. 
*/
fn process_fuel(propellant: String) {
    // propellant takes ownership of the String data stored on the heap
    println!("Processing propellant {}", propellant);
}
```
Вывод
```
Processing propellant MU-RF
```

> Как разработчик Node.js следует помнить, что переменные, связанные с данными изменяемой длины (то есть хранящимися в куче), НЕ содержат сами данные, а являются указателем на эти данные в памяти. Указатель является ссылкой на данные, но НЕ самими данными, он описывает данные и способы их получения.

Чтобы сохранить владение строкой переменной rocket_fuel, вы можете клонировать данные: 

```rust
fn main() {
    let rocket_fuel = String::from("MU-RF");
    // ownership of the string his kept by rocket_fuel variable,
    // a clone/copy of the string data is passed as argument
    process_fuel( rocket_fuel.clone() );
    
    // no panic because rocket_fuel stills own the string data
    println!("rocket_fuel is {}", rocket_fuel);
}
fn process_fuel(propellant: String) {
    // propellant takes ownership of the String data clone
    // mutations on the clone of course will not affect the original data (but remember that you will need to declare mut in function signature)
    println!("Processing propellant {}", propellant);
}
```
Вывод
```
Processing propellant MU-RF
rocket_fuel is MU-RF
```

Если вы не хотите копировать данные и вам нужна исходная переменная, чтобы сохранить право собственности на данные, вы можете передать право собственности обратно, когда функция будет выполнена: 

```rust
fn main() {
    let mut rocket_fuel = String::from("MU-RF");
    // of course, you can declare again without mut
    rocket_fuel = process_fuel(rocket_fuel);
    println!("rocket_fuel is {}", rocket_fuel);
}
// notice the mut on the parameter declaration, to be able to mutate the data passed inside the function scope
// notice the String return type and the last line without a semicolon
fn process_fuel(mut propellant: String) -> String {
    println!("Processing propellant {}", propellant);
    
    propellant.push_str("-super");
    
    propellant
}
```
Ouput
```
Processing propellant MU-RF
rocket_fuel is MU-RF-super
```

## Заимствование

Все эти вещи о праве собственности - это здорово, но что, если вам не нужно передавать право собственности, вам просто нужны данные: 

```rust
fn main() {
    // BORROWING: access data without taking ownership
    // create a reference to the variable you want to borrow
    // using borrow operator "&"
    let rocket_fuel = String::from("MU-RF");
    // notice the borrow operator to create a reference
    // the function expects a reference, not a value
    let length = process_fuel(&rocket_fuel);
    println!("rocket_fuel is {}, length: {}", rocket_fuel,length);
}
// notice the borrow operator in parameter type
// we expect propellant to be a reference to a string,
// not a string value (/pointer to it...)
fn process_fuel(propellant: &String) -> usize {
    // propellant is a reference to the variable that points to the string data, to borrow the data
    // again, NO SHALLOW COPY here because propellant borrows the pointer, it doesn't contain the pointer itself (let's say that it's a pointer to another pointer...)
    println!("Processing propellant {}", propellant);
    let length = propellant.len();
    length
    // when the propellant variable goes out of scope at the end of the function, 
    // the string data is still on the heap because it remains owned by the original variable (rocket_fuel)
}
```
Вывод
```
Processing propellant MU-RF
rocket_fuel is MU-RF, lenght: 5
```

В Rust данные чаще всего передаются по ссылке (заимствованы), чем по значению (передача права собственности). Но вам все равно нужно знать, какая переменная действительно владеет данными.

## Мутация заимствованных данных 

```rust
fn main() {
    // notice it is a mutable variable
    let mut rocket_fuel = String::from("MU-RF");
    // notice we pass a mutable reference (&mut <TYPE>)
    let length = process_fuel(&mut rocket_fuel);
    println!("rocket_fuel is {}, length: {}", rocket_fuel, length);
}
// notice the &mut in the parameter list
// we expect propellant to be a mutable reference to a String
fn process_fuel(propellant: &mut String) -> usize {
    println!("Processing propellant {}", propellant);
    // mutating borrowed data
    propellant.push_str("333");
    let length = propellant.len();
    length
}
```
Вывод
```
Processing propellant MU-RF
rocket_fuel is MU-RF333, lenght: 8
```

## Ограничение на заимствованные данные

После создания изменяемой ссылки на переменную вы не можете создавать другие ссылки на нее в той же области.
Это предотвращает гонку данных, которая возникает, когда несколько ссылок могут обращаться к одним и тем же данным и изменять их.
Гонка данных - распространенная проблема при написании программ, обрабатывающих несколько потоков, которые выполняются одновременно (независимо от языка программирования - даже JavaScript с Node.js [который должен быть однопоточным ... но расширенные варианты использования допускают многопроцессорность и многопоточность ... знать ваши инструменты!]).
Это ограничение позволяет Rust предотвращать скачки данных во время компиляции.
Подводя итог (обратите внимание на фигурные скобки, обозначающие разные области действия): 

```rust
fn main() {
    // IMMUTABLE references = as many as you want per scope
    {
        let something = String::from("a thing");
        let one_ref = &something;
        let another_ref = &something;
        let some_ither_ref = &something;
        println!("{:?}", (one_ref, another_ref, some_ither_ref));
    }
    // MUTABLE references = ONE per scope
    {
        let mut something_changing = String::from("another thing");
        let the_one_and_only_ref = &mut something_changing;
        // CANNOT create other references to "something_changing"
        // NO MATTER if mutable or immutable
        // the following will make the program panic
        // let trying_ref = &something_changing;
        // let trying_other_ref = &mut something_changing;
        println!("It's only {}", the_one_and_only_ref);
    }
}
```
Вывод
```
("a thing", "a thing", "a thing")
It's only another thing
```
## Висячие ссылки 

```rust
fn main() {
    let everything = create_something();
    // "everything" is a stale reference pointing to nothing
    // the program will not compile
    println!("everything is {}", everything);
}
// notice the return type
fn create_something() -> &String {
    let new_thing = String::from("new in town");
    // this is a dangling reference because:
    // new_thing goes out of scope at the end of the function
    // returning a reference to that variable does not make sense
    // because the data will have been dropped from memory because not owned anymore by a variable
    &new_thing
}
```
Вывод

программа не будет компилироваться из-за висящей ссылки. Решение заключается в созданной строке вместо ссылки на нее: 

```rust
fn main() {
    let everything = create_something();
    println!("everything is {}", everything);
}
fn create_something() -> String {
    let new_thing = String::from("new in town");
    new_thing
}
```
Вывод
```
everything is new in town
```
## Структуры (также известные как структуры)

- используется для группировки нескольких связанных элементов смешанных типов данных
- элементы названы (в отличие от кортежей, в которых они упорядочены)
- два вида структур (обычная структура и кортежная структура) 

```rust
// tuple struct
// used to store a collection of mixed data without named fields
// used to be distinguished as a specific type 
// (not just a regular tuple)
struct Signal(u8, bool, String);

// regular struct
// struct names are capitalized 
// like classes in JavaScript and OOP generally
struct Car {
    // fields of the struct
    model: String,
    year: String,
    used: bool,
}
// method: functions/subroutines associated to a struct
// methods are defined within the context of a struct
// the first parameter of a method is the reference to a struct instance
impl Car {
    // construct car
    fn new(m: &str, y: &str) -> Car {
        Car {
            model: m.to_string(),
            year: y.to_string(),
            used: false,
        }
    }
    // self is equivalent to "this" is JavaScript
    fn serialize(&self) -> String {
        format!(
            "model: {} - year: {} - used: {}",
            self.model, self.year, self.used
        )
    }
    // mutate state
    fn marked_used(&mut self) {
        self.used = true;
    }
}
struct Position {
  latitude: f64,
  longitude: f64
}
fn print_signal(s: &Signal) {
    println!("s1 is {}, {}, {}", s.0, s.1, s.2);
}
fn main() {
    let mut pos_1 = Position {
        latitude: 27.299112,
        longitude: 95.387110,
    };
    println!(
      "pos_1 is {:.3}, {:.3}", 
      pos_1.latitude, 
      pos_1.longitude
    );
    pos_1.latitude = 23.1111;
    println!(
      "pos_1 is now {:.3}, {:.3}",
      pos_1.latitude, 
      pos_1.longitude
    );
    let mut s1 = Signal(0, true, String::from("ok"));
    // fields of a tuple struct are accessed like regular tuples values
    // using their index
    // remember tuple structs do not have named fields
    print_signal(&s1);
    s1.0 = 23;
    s1.1 = false;
    s1.2 = String::from("NETERR");
    println!("s1 is now {}, {}, {}", s1.0, s1.1, s1.2);
    let car_1 = Car::new("QBC", "2133");
    println!("car_1 is a {} of {}", car_1.model, car_1.year);
    let is_used = if car_1.used == true {
        "used"
    } else {
        "brand new"
    };
    println!("car_1 is {}", is_used);
    println!("car_1 is {}", car_1.serialize());
    let mut car_2 = Car::new("ZZ7", "2042");
    println!("car_2 is a {}", car_2.serialize());
    car_2.marked_used();
    println!("car_2 is now {}", car_2.serialize());
}
```
Вывод
```
pos_1 is 27.299, 95.387
pos_1 is now 23.111, 95.387
s1 is 0, true, ok
s1 is now 23, false, NETERR
car_1 is a QBC of 2133
car_1 is brand new
car_1 is model: QBC - year: 2133 - used: false
car_2 is a model: ZZ7 - year: 2042 - used: false
car_2 is now model: ZZ7 - year: 2042 - used: true
```

Подробнее о структурах: 

```rust
// need to use debug and clone traits 
// to be able to print the struct instance with debug operator
// to be able to clone an instance of the struct
// more on traits later
#[derive(Debug, Clone)]
struct Spaceship {
    name: String,
    crew: u8,
    propellant: f64,
}
// methods are defined within a impl block
impl Spaceship {
    fn get_name(&self) -> &str {
      // transform string to string slice with borrow operator
      &self.name
    }
    fn add_fuel(&mut self, gallons: f64) {
        self.propellant += gallons;
    }
    // this is an associated function 
    // associated to the struct data type
    // similar to method but no &self reference
    // used for functions related to the struct in general, 
    // not a specific instance 
    // (like class static methods in Object orientation)
    // commonly used to construct instances of struct
    fn new(name: &str) -> Spaceship {
      Spaceship {
        name: String::from(name),
        crew: 11,          // default value for all instances
        propellant: 0.0    // default value for all instances
      }
    }
}
fn main() {
    let mut spaceship1 = Spaceship {
        name: String::from("spaceship1"),
        crew: 123,
        propellant: 1234567.654,
    };
    // accessing field value
    println!("spaceship1 has {} members.", spaceship1.crew);
    spaceship1.crew = 99;
    println!(
        "After attack, spaceship1 has now {} members.",
        spaceship1.crew
    );
    println!("spaceship1 is {:?}", spaceship1);
    // by default struct data is stored on the STACK
    // if struct contains HEAP-stored data (like a string),
    // the pointer is stored on the STACK and the data on the HEAP
    // create struct instance from fields of other instance
    //  (like spread operator in JS but two dots instead of three)
    // it is called struct update syntax 
    // it allows to create new instances
    //  by copying the values of fields of an existing instance
    // (except for fields explicitly set in new instance)
    let spaceship2 = Spaceship {
        name: String::from("Galactos"),
        ..spaceship1
    };
    println!("spaceship2 is {:?} members.", spaceship2);
    // when modifying the copied struct instance 
    // it will not affect new instances after the copy 
    // (unlike JavaScript references that are kept,
    // when spreading objects with arrays...)
    spaceship1.name = String::from("Battlestar");
    spaceship1.crew = 78;
    assert_eq!(spaceship2.crew, 99);
    println!("spaceship1 is {:?}", spaceship1);
    println!("spaceship2 is {:?}", spaceship2);
    // to copy all fields, even the heap based ones,
    // you need to create a copy of the struct instance
    // otherwise when using struct update syntax it moves ownership of heap-based data
    // meaning that you could no longer use the associated fields from the copied instance
   let mut spaceship3 = Spaceship {
        // need to manually implement the clone trait 
        // on the struct definition (see above)
        ..spaceship1.clone()
    };
    println!("spaceship3 is {:?}", spaceship3);
    let name = spaceship3.get_name();
    println!("name is {}", name);
    println!("spaceship3 propellant is {}", spaceship3.propellant);
    
    spaceship3.add_fuel(4567.113);
    
    println!(
      "After going to space refuel station, spaceship3 propellant is now {}", 
      spaceship3.propellant
    );
    // to call an assocaited function, use the path operator (::)
    let spaceship4 = Spaceship::new("Serenity");
    assert_eq!(spaceship4.crew, 11);
}
```

## Перечисления

```rust
// defines a data type with multiple possible variants
enum Controller {
    Turbo,
    Up,
    Down,
    Left,
    Right,
    X,
    Y,
    A,
    B,
}
fn push_button_notify(c: &Controller) {
    // pattern matching (equivalent to switch in JavaScript)
    match c {
        Controller::Turbo => println!("Turbo button pushed."),
        Controller::Up => println!("Up button pushed."),
        Controller::Down => println!("Down button pushed."),
        Controller::Left => println!("Left button pushed."),
        Controller::Right => println!("Right button pushed."),
        Controller::Y => println!("Y button pushed."),
        Controller::X => println!("X button pushed."),
        Controller::A => println!("A button pushed."),
        Controller::B => println!("B button pushed."),
    }
}
fn main() {
    let secret_push_combo = [
        Controller::Up,
        Controller::Left,
        Controller::A,
        Controller::Turbo,
        Controller::Y,
        Controller::B,
        Controller::Turbo,
        Controller::Down,
        Controller::Right,
        Controller::X,
    ];
    for push in secret_push_combo.iter() {
        push_button_notify(push);
    }
}
```
Вывод

```
Up button pushed.
Left button pushed.
A button pushed.
Turbo button pushed.
Y button pushed.
B button pushed.
Turbo button pushed.
Down button pushed.
Right button pushed.
X button pushed.
```

## Обрезка строк 

```rust
fn main() {
    let test1 = "This is test1.        ";
    assert_eq!(trim_space(&test1), "This is test1.");
    let test2 = "        This is test2.";
    assert_eq!(trim_space(&test2), "This is test2.");
    let test3 = "        This is test3.               ";
    assert_eq!(trim_space(&test3), "This is test3.");
    let test4 = "This is test4.";
    assert_eq!(trim_space(&test4), "This is test4.");
    let test5 = "        ";
    assert_eq!(trim_space(&test5), "");
    let test6 = "";
    assert_eq!(trim_space(&test6), "");
    let test7 = "    😸   ";
    assert_eq!(trim_space(&test7), "😸");
    let test8 = "     😸😸 test8.   ";
    assert_eq!(trim_space(&test8), "😸😸 test8.");
    let test9 = "     😸😸 test9. 😸😸   ";
    assert_eq!(trim_space(&test9), "😸😸 test9. 😸😸");
    // note: strings in Rust have a trim() method
    let test10 = " 😸This is the last test😸.😸      ";
    let trimmed_message = test10.trim();
    assert_eq!(trimmed_message, "😸This is the last test😸.😸");
}
fn trim_space(s: &str) -> &str {
    let bytes = s.as_bytes();
    let mut result = "";
    // trim leading spaces by finding first non-space character
    // iterate over byte representation of the string slice
    // we do this because, we use am intermediary string slice
    for (index, &item) in bytes.iter().enumerate() {
        if item != b' ' {
            result = &s[index..];
            break;
        }
    }
    let bytes = result.as_bytes();
    // trim trailing spaces by finding last non-pace character
    for t in bytes.iter().enumerate() {
        let index = t.0;
        let reverse_index = result.len() - index;
        if bytes[reverse_index - 1] != b' ' {
            result = &result[..reverse_index];
            break;
        }
    }
    result
}
```

Другое возможное решение: 

```rust
fn trim_space(s: &str) -> &str {
    let mut start = 0;
    let mut end = 0;
    // iterate over the characters of the string slice
    for (index, character) in s.chars().enumerate() {
        if character != ' ' {
            start = index;
            break;
        }
    }
    for (index, character) in s.chars().rev().enumerate() {
        if character != ' ' {
            end = s.len() - index;
            break;
        }
    }
    &s[start..end]
}
```

## Стандартная библиотека

- документация: https://doc.rust-lang.org/std/ 

```rust
// the use statement is used to import crates/libraries
// it brings a module path into the scope of a program
// some modules of the standard library are not part of the Rust language itself 
// (meaning, you need to import them - like the "fs" module in Node.js - otherwise the compiler will not know what they mean)
// the standard library is available to all programs by default
// the prelude is a list of things that are automatically imported into every Rust program
// it does not import the entire std library (only the most common standard modules)
// if a module is not included into the prelude, you need to manually import it
use std::thread;
fn main() {
    let child = thread::spawn(move || 2 + 2);
    let res = child.join();
    println!("res is {}", res.unwrap());
}
```
Вывод
```
res is 4
```

## Анализ стандартного ввода 

```rust
// read command line inputs
use std::io;
fn main() {
  let mut buffer = String::new();
  println!("Enter your name:");
  // ===== access the stdin stream =====
  // the read_line() function will update the buffer with the input string
  // read_line() blocks the execution of the program until something is entered at the command line
  let read_line_result = io::stdin().read_line(&mut buffer);
  println!("read_line_result is {:?}", read_line_result);
  println!("Welcome to Rust, {}", buffer);
  // ===== parse input string ========
  // clear buffer from previous input
  buffer.clear();
  println!("Enter the year when you started learning Rust:");
  let read_line_result = io::stdin().read_line(&mut buffer);
  println!("read_line_result is {:?}", read_line_result);
  // need to trim the input string because contains a newline at the end
  // notice the turbofish operator (::<i32>) to indicate the type of data to parse from the input string (here an i32 integer)
  // parse() returns a Result enum
  // Result enum allow to handle errors (more on this later)
  // use the unwrap function to extract the value
  let start_year = buffer.trim().parse::<i32>().unwrap();
  println!("You started your Rust journey in {}", start_year);
  // without using the  turbofish operator, indicate the destination type as the variable type
  let start_year: i32 = buffer.trim().parse().unwrap();
  println!("You will be an expert in {}", start_year + 5);
}
```
Output (> marks command line inputs)
```
Enter your name:
> Florian
read_line_result is Ok(8)
Welcome to Rust, Florian
Enter the year when you started learning Rust:
> 2021
read_line_result is Ok(6)
You started your Rust journey in 2021
You will be an expert in 2026
```

## крэйти

- сборник файлов с исходным кодом
- реестр крэйтов: crates.io (эквивалент npmjs.com для модулей Node.js)
- два вида:
     - бинарные крэйти содержат исходный код для компиляции для выполнения программы
     - крэйти библиотек содержат код для выполнения другими программами

чтобы использовать сторонние крэйти, добавьте имя и версию крэйта в файле Cargo.toml в разделе [dependencies] (внешние крэйти): 

```toml
[package]
name = "crates"
version = "0.1.0"
authors = ["Florian GOTO <you@example.com>"]
edition = "2018"
# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
[dependencies]
# add the random number generation crate
rand = "0.8.3"
```

Cargo автоматически загрузит крэйти в разделе [зависимости] вместе со своими собственными зависимостями перед компиляцией для использования загруженного крэйта. 

```rust
use rand;
fn main() {
  let random_number = rand::random::<u8>();
  println!("random_number is {}", random_number);
}
```

Вывод
```
random_number is 97
```

- если вы используете определенную функцию крэйта, укажите ее непосредственно в операторе использования (компилятор предупредит вас о конфликтах имен с локально определенным кодом): 

```rust
// import only the random() function of the rand crate
use rand::random;
// import everything from the rand crate prelude
// use rand::prelude::*;
fn main() {
  let random_number = random::<u8>();
  println!("random_number is {}", random_number);
}
// cannot have a local function named random in the same scope
// the program will not compile if the following code is added because of the name collision
// fn random() -> u8 { 
//   123 
// }
```

## Новый взгляд на игру в угадывание

```rust
use rand::prelude::*;
use std::io;
fn main() {
    const MAX_RETRIES: i8 = 3;
    // generate random number between specified range
    let random_number: i8 = thread_rng().gen_range(1..101);
    let mut buffer = String::new();
    let mut retry_count = 0;
    println!("Guess a number between 1 and 100:");
    while retry_count < MAX_RETRIES {
       // clear previous guess
       buffer.clear();
       io::stdin()
            .read_line(&mut buffer)
            .expect("Failed to read line");
       // expect() is equivalent to unwrap() 
       // used to get value from Result enum
       // it allows to print a custom message when an error occurs
       let guess = buffer.trim()
                         .parse::<i8>()
                         .expect("Failed to parse number");
       if guess < random_number {
            retry_count += 1;
            println!(
                "too low. {}",
                if retry_count < MAX_RETRIES {
                    "Try again:"
                } else {
                    ""
                }
            );
        }
        if guess > random_number {
            retry_count += 1;
            println!(
                "too high. {}",
                if retry_count < MAX_RETRIES {
                    "Try again:"
                } else {
                    ""
                }
            );
        }
        if guess == random_number {
            println!("correct!");
            return;
        }
    }
    println!("random_number was {}", random_number);
}
```

Альтернативная реализация: 

```rust
use rand::prelude::*;
use std::io;
fn main() {
    guess_with_infinite_retry();
}
fn guess_with_infinite_retry() {
    let secret_number = thread_rng().gen_range(1..101);
    println!("Guess a number between 1 and 100:");
    loop {
        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");
        let guess: u32 = guess.trim()
                              .parse()
                              .expect("Failed to parse guess");
        if guess < secret_number {
            println!("guess too low, guess higher:");
        } else if guess > secret_number {
            println!("guess too high, guess lower:");
        } else {
            println!("you found it!");
            break;
        }
    }
}
```

Альтернативная реализация с «продолжить игру»: 

```rust
use rand::prelude::*;
use std::io;
fn main() {
    guess_with_limited_retry(5);
}
fn guess_with_limited_retry(max_retries: i8) {
    // generate random number between specified range
    let random_number: i8 = thread_rng().gen_range(1..101);
    let mut buffer = String::new();
    let mut new_game = true;
    while new_game == true {
        println!("Guess a number between 1 and 100:");
        let mut retry_count = 0;
         while retry_count < max_retries {
            buffer.clear();
            io::stdin()
                .read_line(&mut buffer)
                .expect("Failed to read line");
            let guess = buffer.trim()
                              .parse::<i8>()
                              .expect("Failed to parse number");
            if guess < random_number {
                retry_count += 1;
                println!(
                    "too low. {}",
                    if retry_count < max_retries {
                        "Try again:"
                    } else {
                        ""
                    }
                );
            }
            if guess > random_number {
                retry_count += 1;
                println!(
                    "too high. {}",
                    if retry_count < max_retries {
                        "Try again:"
                    } else {
                        ""
                    }
                );
            }
            if guess == random_number {
                println!("correct!");
                break;
            }
        }
      println!("random_number was {}", random_number);
      println!("do you want to continue playing ? (y/n)");
      let mut try_again = String::new();
      io::stdin()
         .read_line(&mut try_again)
         .expect("Failed to read line");
      let try_again: char = try_again
         .trim()
         .parse()
         .expect("Failed to parse try again answer.");
      if try_again == 'y' {
            new_game = true;
      } else if try_again == 'n' {
            new_game = false;
      } else {
            println!("answer with y or n.")
      }
    }
}
```

## Аргументы командной строки 

```rust
use std::env;
fn main() {
    // check for required number of arguments
    if env::args().len() <= 2 {
        println!("this program requires at least two arguments");
        return;
    }
    // args() returns an iterator over arguments passed to the program executable binary file
    // use enumerate() to get input args and their index
    // first argument is the executable path (but not always)
    for (index, arg) in env::args().enumerate() {
        println!("argument {} is {}", index, arg);
    }
    // get argument at specific index
    let arg2 = env::args().nth(2).unwrap();
    println!("\narg2 is {}", arg2);
}
```
Вывод
```
> cargo run toto "titi.txt" --flag -t
argument 0 is target/debug/command_line_args
argument 1 is toto
argument 2 is titi.txt
argument 3 is --flag
argument 4 is -t
arg2 is titi.txt
Reading content from file
create a fruits.txt file in your project folder with the following content:
apple
avocado
banana
cherry
orange
mango
coconut
pear
lemon
```

```rust
use std::fs;
use std::path::Path;
fn main() {
    let path = Path::new("./fruits.txt");
    let contents = fs::read_to_string(path)
                      .expect("Failed to read file.");
    println!("contents is: {}", contents);
    // process individual lines
    for line in contents.lines() {
        println!("line is {}", line);
    }
    // read file as bytes
    let contents: Vec<u8> = fs::read(path)
                               .expect("Failed to read file.");
    println!("\ncontents is: {:?}", contents);
}
```

Вывод

```
contents is: apple
avocado
banana
cherry
orange
mango
coconut
pear
lemon
line is apple
line is avocado
line is banana
line is cherry
line is orange
line is mango
line is coconut
line is pear
line is lemon
contents is: [97, 112, 112, 108, 101, 10, 97, 118, 111, 99, 97, 100, 111, 10, 98, 97, 110, 97, 110, 97, 10, 99, 104, 101, 114, 114, 121, 10, 111, 114, 97, 109, 103, 101, 10, 109, 97, 110, 103, 111, 10, 99, 111, 99, 111, 110, 117, 116, 10, 112, 101, 97, 114, 10, 108, 101, 109, 111, 110, 10]
```

## Запись содержимого в файл 

- скопируйте файл fruit.txt из предыдущего раздела 

```rust
use std::fs;
use std::path;
// import the Write trait of the io module prelude (more on traits later)
use std::io::prelude::*;
fn main() {
    let path = path::Path::new("./file.txt");
    let mut contents = String::new();
    contents.push_str("this is line 1\n");
    contents.push_str("this is line 2\n");
    contents.push_str("this is line 3\n");
    // === write/replace file content ===
    fs::write(path, contents)
       .expect("Failed to write to file.");
    // === append content to file ===
    let path = path::Path::new("./fruits.txt");
    // new() function of the OpenOptions struct in the fs module
    let mut file = fs::OpenOptions::new()
        .append(true)
        .open(path)
        .expect("Failed to open file.");
    // uses the imported Write trait
    // expects an array of bytes as argument
    // notice the b in front of the string to convert to byte array
    file.write(b"\nolive\n")
        .expect("Failed to write to file.");
}
```

## Поиск фруктов

- скопируйте файл fruit.txt из предыдущих разделов в папку вашего проекта
- создадим программу, которая ищет фрукты в списке
- если фрукт есть в списке, он возвращает сообщение с его позицией в списке
- если не найден, то добавляется в список 

```rust
use std::env;
use std::fs;
use std::io::prelude::*;
use std::path::Path;
fn main() {
    if env::args().len() != 3 {
        println!("This programs takes two arguments");
        std::process::exit(1);
    }
    let file_path = env::args().nth(1).expect("Failed to get file path");
    let search_name = env::args().nth(2).expect("Failed to get search name");
    let path = Path::new(&file_path);
    let fruits = fs::read_to_string(path).expect("Failed to read file");
    for (index, fruit) in fruits.lines().enumerate() {
        if fruit == search_name {
            println!(
                "{} is the {}{} fruit in the list",
                search_name,
                index + 1,
                if index == 0 {
                    "st"
                } else if index == 1 {
                    "nd"
                } else if index == 2 {
                    "rd"
                } else {
                    "th"
                }
            );
            return;
        }
    }
    println!("{} is not in the fruit list", search_name);
    let mut file = fs::OpenOptions::new()
        .append(true)
        .open(path)
        .expect("Failed to open file.");
    let mut new_fruit = search_name.clone();
    let fruits_bytes = fruits.as_bytes();
    if fruits_bytes[fruits_bytes.len() - 1] != b'\n' {
        // insert newline at start of string
        new_fruit.insert(0, '\n');
    }
    new_fruit.push('\n');
    file.write(new_fruit.as_bytes())
        .expect("Failed to write to file.");
    println!("{} was added to fruit list", search_name);
}
```
Вывод

```
> cat fruits.txt 
apple
avocado
banana
cherry
orange
mango
coconut
pear
lemon
lime
pineapple
papaya
grape
guava
kiwi
elderberry
raspberry
tomato
> cargo run -q fruits.txt tomato
tomato is the 18th fruit in the list
> cargo run -q fruits.txt avocado
avocado is the 2nd fruit in the list
> cargo run -q fruits.txt peach
peach is not in the fruit list
peach was added to fruit list
cargo run -q fruits.txt peach
peach is the 19th fruit in the list
```

## Фабрики форм 

```rust
struct Rectangle {
    width: f64,
    height: f64,
}
struct Circle {
    radius: f64,
}
impl Rectangle {
    fn new(width: f64, height: f64) -> Rectangle {
        // notice the shorthand notation 
        // when creating instance of struct
        // because method params have same names as struct fields
        Rectangle { width, height }
    }
    fn get_area(&self) -> f64 {
        self.height * self.width
    }
    fn scale(&mut self, scalar: f64) {
        self.width *= scalar;
        self.height *= scalar;
    }
}
impl Circle {
    fn new(radius: f64) -> Circle {
        Circle { radius }
    }
    fn get_area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
    fn get_diameter(&self) -> f64 {
        self.radius * 2.0
    }
}
fn main() {
    let mut rect = Rectangle::new(1.2, 3.4);
    assert_eq!(rect.get_area(), 4.08);
    rect.scale(0.5);
    assert_eq!(rect.get_area(), 1.02);
    let expected = 16.0 * std::f64::consts::PI;
    assert_eq!(circ.get_area(), expected);
    println!("Test passed!");
}
```
Вывод
```
Test passed!
```

## Определение структур с дженериками

```rust
// abstract stand-ins for concrete data types or other properties
// can be used with structs, functions, methods, etc. to help eliminate duplicate code
// gives flexibility to data types
// defined with<T>(T: generic type variable)
// the name of the generic variable is arbitrary, could be <Toto>
// similar to generics in TypeScrypt
#[derive(Debug)]
struct Rectangle<T> {
    width: T,
    height: T,
}
// multiple generic types
#[derive(Debug)]
struct Shape<T, U> {
    width: T,
    height: U,
}
fn main() {
    // creating rectangle with f64 data
    let rect = Rectangle {
        width: 1.2,
        height: 3.4,
    };
    println!("rect is {:?}", rect);
    // creating rectangle with u32 data
    let rect = Rectangle {
        width: 5,
        height: 11,
    };
    println!("rect is {:?}", rect);
    // creating rectangle with u8 data
    // notice we add u8 as a suffix to the number
    let rect = Rectangle {
        width: 7u8,
        height: 23u8,
    };
    println!("rect is {:?}", rect);
    // creating shape with u16 and f32 data
    let rect = Shape {
        width: 456u16,
        height: 78.54f32,
    };
    println!("rect is {:?}", rect);
}
```
Вывод
```
rect is Rectangle { width: 1.2, height: 3.4 }
rect is Rectangle { width: 5, height: 11 }
rect is Rectangle { width: 7, height: 23 }
rect is Shape { width: 456, height: 78.54 }
```

- Обобщения - это абстракция с нулевой стоимостью = более простое программирование без снижения производительности во время выполнения.
- Компилятор Rust использует мономорфизацию для дженериков = заменяет заполнители конкретными типами данных
- Во время компиляции код будет включать конкретные типы данных, а не общую абстракцию = если вы создаете Rectangle с данными f64, скомпилированный код будет иметь определение структуры для Rectangle с полями f64 и столько определений конкретных типов, сколько при создании экземпляров структуры.
- Исходный код будет использовать общие типы данных, но скомпилированный код будет иметь конкретные типы данных, что не повлияет на производительность во время выполнения (никаких догадок о типах данных).

## Определения методов с обобщениями

```rust
#[derive(Debug)]
struct Rectangle<T> {
    width: T,
    height: T,
}
#[derive(Debug)]
struct Shape<T, U> {
    width: T,
    height: U,
}
// include the generic variable names after the impl keyword
// and after the struct identifier
// impl<T, U> tells the compiler that methods for generic struct definition
impl<T, U> Shape<T, U> {
    fn get_width(&self) -> &T {
        // need to return a reference 
        // because not know by which type<T> will be replaced with
        // remember ownership for fixed / mutable-length data type
        // returning a reference works for both without transfer of ownership
        &self.width
    }
}
// implementing methods for a specific concrete type of Shape
// notice not putting generic types after impl keyword tells the compiler that these methods are for concrete struct defintition
impl Shape<u8, u8> {
    fn get_perimeter(&self) -> u8 {
        (self.height + self.width) * 2
    }
}
fn main() {
    let rect = Rectangle {
        width: 1.2,
        height: 3.4,
    };
    println!("rect is {:?}", rect);
    let rect = Rectangle {
        width: 5,
        height: 11,
    };
    println!("rect is {:?}", rect);
    let rect = Rectangle {
        width: 7u8,
        height: 23u8,
    };
    println!("rect is {:?}", rect);
    let rect = Shape {
        width: 456u16,
        height: 78.54f32,
    };
    println!("rect is {:?}", rect);
    println!("rect width is {:?}", rect.get_width());
    let rect = Shape {
        width: 54u8,
        height: 32u8,
    };
    // get_perimeter() method is defined on this instance of Shape 
    // because the fields are both u8 data
    // otherwise this method would be not be found
    println!("rect perimter is {:?}", rect.get_perimeter());
}
```
Вывод
```
rect is Rectangle { width: 1.2, height: 3.4 }
rect is Rectangle { width: 5, height: 11 }
rect is Rectangle { width: 7, height: 23 }
rect is Shape { width: 456, height: 78.54 }
rect width is 456
rect perimter is 172
```

## Определения функций с дженериками

```rust
// restrict the generic type
// to types that implement the Trait for values that can be compared for a sort-order (PartialOrd)
// PartialOrd trait is included in the std crate prelude (no need for full path)
// otherwise, how would the compiler know if the concrete types can be compared using > ?
fn get_biggest<T: PartialOrd>(n1: T, n2: T) -> T {
    if n1 > n2 {
        n1
    } else {
        n2
    }
}
fn main() {
    println!("biggest is {}", get_biggest(23u8, 121u8));
    println!("biggest is {}", get_biggest(12345, 121));
    println!("biggest is {}", get_biggest(123.45, 1.21));
}
```
Вывод
```
biggest is 121
biggest is 12345
biggest is 123.45
```

## Тип данных Box

```rust
// Box<T> used to store data on the heap instead of the stack
// it consists of a pointer on the stack
// which points to a memory chunk on the heap
// that has been allocated large enough to hold<T> data
// Boxes are considered smart pointers
// provide more functionality than references:
// Box<T> has ownership of the data it points to
// Box<T> deallocates the heap memory when it goes out of scope
// Box data type is used to store a type whose size cannot be known at compile time
// but a size is still required
// ex: recursive types
// ex of recursive type: a struct which includes another struct of the same type as one of its fields (think about Iterators - like in JavaScript)
// you don't know the depth of layers at compile time and therefore not know the size of the overall structure
// another use case for boxes is to transfer ownership of data rather copying it on the stack
// to avoid copying large amounts of stack data
// potential performance improvement by moving data to heap where can be more easily transferred (because of pointers)
use std::mem;
#[derive(Debug)]
struct Team {
    name: String,
    size: u8,
    capacity: f64,
    domain: String,
}
fn main() {
    // remember the struct lives on the stack
    // except for mutable-length fields (ex: String) that leave on the heap
    let lions = Team {
        name: String::from("The lions"),
        size: 7,
        capacity: 11.5,
        domain: String::from("Authentication"),
    };
    println!("lions is {:?}", lions);
    // mem::size_of_val() gives the size in byte of what the given reference points to
    println!(
      "lions size is on stack {} bytes", 
      mem::size_of_val(&lions)
    );
    // this is not a copy operation, ownership is moved
    // the lions variable looses ownership of the struct
    // the lions variable can no longer be used
    // the struct instance is transferred to to heap location allocated by the box
    // the boxed_lions variable becomes the new owner of the struct
    // through the box smart pointer that lives on the stack
    let boxed_lions: Box<Team> = Box::new(lions);
    println!("boxed_lions is {:?}", boxed_lions);
    // printing size in bytes of the box pointer
    println!(
        "boxed_lions size is on stack {} bytes",
        mem::size_of_val(&boxed_lions)
    );
    // printing size in byte of the struct instance
    // need to pass a reference to the dereferenced box pointer (WTF...) to mem::size_of_val()
    // using the dereference operator "*" before the box pointer
    // referencing a pointer will represent the pointed-to location / data:
    // &*boxed_lions is a reference to the data on the heap rather than the pointer on the stack
    println!(
        "boxed_lions team size is on heap {} bytes",
        mem::size_of_val(&*boxed_lions)
    );
    // moving back the struct instance on the stack
    // by dereferencing the box pointer
    let unboxed_lions: Team = *boxed_lions;
    println!("unboxed_lions is {:?}", unboxed_lions);
    println!(
        "unboxed_lions team size is on stack {} bytes",
        mem::size_of_val(&unboxed_lions)
    );
}
```

Вывод

```
lions is Team { name: "The lions", size: 7, capacity: 11.5, domain: "Authentication" }
lions size is on stack 64 bytes
boxed_lions is Team { name: "The lions", size: 7, capacity: 11.5, domain: "Authentication" }
boxed_lions size is on stack 8 bytes
boxed_lions team size is on heap 64 bytes
unboxed_lions is Team { name: "The lions", size: 7, capacity: 11.5, domain: "Authentication" }
unboxed_lions team size is on stack 64 bytes
```

## Суммируем boxes

```rust
use std::ops::Add;
// restrict generic type T to concrete types that implement the Add trait (like u32, f64, etc.)
fn sum_boxes<T: Add<Output = T>>(b1: Box<T>, b2: Box<T>) -> Box<T> {
    let unboxed_b1 = *b1;
    let unboxed_b2 = *b2;
    let sum = unboxed_b1 + unboxed_b2;
    Box::new(sum)
}
fn main() {
    let box1 = Box::new(11);
    let box2 = Box::new(44);
    let sum_box = sum_boxes(box1, box2);
    assert_eq!(*sum_box, 55);
    let box1 = Box::new(1.1);
    let box2 = Box::new(4.4);
    let sum_box = sum_boxes(box1, box2);
    assert_eq!(*sum_box, 5.5);
    let box1 = Box::new(1.1f32);
    let box2 = Box::new(4.4f32);
    let sum_box = sum_boxes(box1, box2);
    assert_eq!(*sum_box, 5.5f32);
    let box1 = Box::new(11u8);
    let box2 = Box::new(44u8);
    let sum_box = sum_boxes(box1, box2);
    assert_eq!(*sum_box, 55u8);
    let box1 = Box::new(11u16);
    let box2 = Box::new(44u16);
    let sum_box = sum_boxes(box1, box2);
    assert_eq!(*sum_box, 55u16);
    println!("tests passed!");
}
```
Вывод
```
test passed!
```
## Компактная версия 

```rust
use std::ops::Add;
fn sum_boxes<T: Add<Output = T>>(b1: Box<T>, b2: Box<T>) -> Box<T> {
    Box::new(*b1 + *b2)
}
fn main() {
    assert_eq!(*sum_boxes(Box::new(11), Box::new(44)), 55);
    assert_eq!(*sum_boxes(Box::new(1.1), Box::new(4.4)), 5.5);
    assert_eq!(*sum_boxes(Box::new(1.1f32), Box::new(4.4f32)), 5.5f32);
    assert_eq!(*sum_boxes(Box::new(11u8), Box::new(44u8)), 55u8);
    assert_eq!(*sum_boxes(Box::new(11u16), Box::new(44u16)), 55u16);
    println!("tests passed!");
}
```
Более сложное ограничение универсального типа 
```rust
use std::ops::{Add, MulAssign};
// remove the Copy trait restriction and see what happens
// more on this later
fn sum_boxes<T: Add<Output = T> + MulAssign + Copy>(
    mut b1: Box<T>, 
    b2: Box<T>
) -> Box<T> {
    *b1 *= *b2;
    Box::new(*b1 + *b2)
}
fn main() {
    let box1 = Box::new(11);
    let box2 = Box::new(44);
    assert_eq!(*sum_boxes(box1, box2), 528);
    let box1 = Box::new(1.1);
    let box2 = Box::new(4.4);
    assert_eq!(*sum_boxes(box1, box2), 9.240000000000002);
    let box1 = Box::new(1.1f32);
    let box2 = Box::new(4.4f32);
    assert_eq!(*sum_boxes(box1, box2), 9.24f32);
    let box1 = Box::new(11u16);
    let box2 = Box::new(44u16);
    assert_eq!(*sum_boxes(box1, box2), 528u16);
    println!("tests passed!");
}
```
## Трэйты
```rust
// trait;
// abstract way to define capabilities for functionality of specific data types
// collection of methods
// representing a set of behaviors necessary to accomplish some task
// data types can implement a trait:
// the type implements those specific methods so they'll be available for use with it
// generics use traits to specify the capabilities of unknown data types:
// the trait act as a bound for which concrete types will be accepted
// as long as these concrete types implement the set of methods
// similar to interfaces in TypeScript and other languages supporting classical OOP
// Rust comes with default common traits (see the std::prelude)
// you can define your custom traits
struct Car {
    model: String,
    brand: String,
    velocity: u16, // km/h
}
struct Plane {
    name: String,
    nick_name: String,
    velocity: u16, // km/h
    crew: u8,
    passengers: u16,
    ceiling: u32,
}
// custom trait
trait Serialization {
    // method signatures
    fn describe(&self) -> String;
}
// implementing trait for a specific type
impl Serialization for Car {
    fn describe(&self) -> String {
        // format! macro returns formatted string
        format!(
            "{} from {} going at {} km/h.",
            self.model, self.brand, self.velocity
        )
    }
}
// implementing trait for another type
impl Serialization for Plane {
    fn describe(&self) -> String {
        format!(
            "{} ({}) going at {} km/h with a crew of {} and up to {} passengers at {} m above.",
            self.name, self.nick_name, self.velocity, self.crew, self.passengers, self.ceiling
        )
    }
}
fn main() {
    let gle_class_coupe = Car {
        model: String::from("GLE-Class Coupe"),
        brand: String::from("Mercedes-Benz"),
        velocity: 280,
    };
    let g6 = Plane {
        name: String::from("Gulfstream G650 "),
        nick_name: String::from("G6"),
        velocity: 956,
        crew: 4,
        passengers: 19,
        ceiling: 15545,
    };
    println!("{}", gle_class_coupe.describe());
    println!("{}", g6.describe());
}
```
Вывод
```
GLE-Class Coupe from Mercedes-Benz going at 280 km/h.
Gulfstream G650  (G6) going at 956 km/h with a crew of 4 and up to 19 passengers at 15545 m above.
```
## Реализация трэйта по умолчанию 

```rust
struct Car {
    model: String,
    brand: String,
    velocity: u16, // km/h
}
struct Plane {
    name: String,
    nick_name: String,
    velocity: u16, // km/h
    crew: u8,
    passengers: u16,
    ceiling: u32,
}
trait Serialization {
    // default trait implementation
    // to avoid having to implement it for each type that uses the trait
    fn describe(&self) -> String {
        String::from("some kind of vehicle.")
    }
}
impl Serialization for Car {
    // specific trait implementation for Car structure
    // overriding the default implementation
    fn describe(&self) -> String {
        format!(
            "{} from {} going at {} km/h.",
            self.model, self.brand, self.velocity
        )
    }
}
// will use the default trait implementation because no specific one
impl Serialization for Plane {}
fn main() {
    let gle_class_coupe = Car {
        model: String::from("GLE-Class Coupe"),
        brand: String::from("Mercedes-Benz"),
        velocity: 280,
    };
    let g6 = Plane {
        name: String::from("Gulfstream G650 "),
        nick_name: String::from("G6"),
        velocity: 956,
        crew: 4,
        passengers: 19,
        ceiling: 15545,
    };
    println!("{}", gle_class_coupe.describe());
    println!("{}", g6.describe());
}
```
Вывод
```
GLE-Class Coupe from Mercedes-Benz going at 280 km/h.
some kind of vehicle.
```
## Производные трэйты 
```rust
// by default new structs do not implement any trait
// you as the programmer give it the traits it needs
// derivable traits provide default implementations for common traits
// gives access to basic functionalities
// instead of having to implement custom traits
// when deriving traits,
// the compiler generates default code for the required methods
// derivable traits are (subject to change as the language evolves):
// Eq
// PartialEq
// Ord
// PartialOrd
// Clone
// Copy
// Hash
// Default - create empty instance of a data type
// Debug - provide formatted debug string
// to derivve traits:  #[ derive( trait_a, trait_b, ..) ]
// default implementation of PartialEq requires all fields of both structs to be equal to return true
// (check documentation  std::cmp::PartialEq)
// default implementation of PartialOrd compares the values of the fields,
// parses the fields in order of definition and
// once found a field that can be compared,
// will return result of comparison
// and not look at other fields (check documentation  std::cmp::PartialOrd)
#[derive(PartialEq, PartialOrd, Debug)]
struct Car {
    model: String,
    brand: String,
    velocity: u16, // km/h
}
fn main() {
    let gle_class_coupe = Car {
        model: String::from("GLE-Class Coupe"),
        brand: String::from("Mercedes-Benz"),
        velocity: 280,
    };
    let gle_class = Car {
        model: String::from("GLE-Class"),
        brand: String::from("Mercedes-Benz"),
        velocity: 218,
    };
    // allowed because of derived PartialEq trait
    println!("Are the cars equal: {}", gle_class_coupe == gle_class);
    // allowed because of derived PartialOrd trait
    // here the name field is compared to assert which one is greater
    println!(
        "Is the coupe greater then the SUV: {}",
        gle_class_coupe > gle_class
    );
    // allowed because of derived Debug trait
    println!("{:?}", gle_class);
}
```
Вывод
```
Are the cars equal: false
Is the coupe greater then the SUV: true
Car { model: "GLE-Class", brand: "Mercedes-Benz", velocity: 218 }
```
## Органичения трейтов
```rust
// when working with generics we can use traits
// in order to stipulate which functionalities the concrete types must implement
// trait bounds force a generic type to implement specific traits
// trait bounds guarantee a generic type will have the necessary behaviors
use core::fmt::Debug;
use std::any;
// To be able to print an item it must implement the Display trait
// but because not all type implement it, we will use the Debug trait
// which is more commonly implemented by default
fn print_type<T: Debug>(it: T) {
    // type_name() returns the name of a type as a string slice
    // by passing the type to the turbofish operator (::<MY_TYPE>)
    println!("{:?} is a {}", it, any::type_name::<T>());
}
fn main() {
    print_type(1);
    print_type(1.2);
    print_type([12, 34]);
}
```
Вывод

```
1 is a i32
1.2 is a f64
[12, 34] is a [i32; 2]
```

## Множественные границы трейтов и предложение where 

```rust
use std::fmt::Display;
// multiple trait bounds are separated by a + operator
// PartialEq/From/Copy are part of the prelude of the std library so no import
// fn compare_and_print<T: Display + PartialEq + From<U>, U: Display + PartialEq + Copy>(a: T, b: U) {
// the below function signature is equivalent to the above 
// but more readable with the were clause to declare trait bounds
fn compare_and_print<T, U>(a: T, b: U)
where
    T: Display + PartialEq + From<U>,
    U: Display + PartialEq + Copy,
{
    // T::from(b) => convert b to type T
    // From trait allows for a type 
    // to define how to create itself from another type.
    // ex: creating a string from string slice
    //      String::From("ttoto")
    if a == T::from(b) {
        println!("{} is equal to {}", a, b);
    } else {
        println!("{} is not equal to {}", a, b);
    }
}
fn main() {
    compare_and_print(22, 55);
    compare_and_print(2.2, 2.2);
}
```
Вывод
```
22 is not equal to 55
2.2 is equal to 2.2
```
## Типы возвращаемых данных и реализованные трейты 
```rust
use std::fmt::Display;
// the return value must implement the Display trait
fn get_displayable() -> impl Display {
    1
}
fn main() {
    println!("Display: {}", get_displayable());
}
```
Вывод
```
Display: 1
```
## Типы возврата и динамическая диспетчеризация с трэйтами 
```rust
use std::fmt::Display;
// dynamic dispatch
// when the retun type cannot be known until runtime
// notice the dyn keyword and the use of a Box pointers
// Returns a value that implements the Display trait, 
// but we do not know which one at compile time.
fn get_dynamic_displayable(a: bool) -> Box<dyn Display> {
    if a {
        // returns u32
        Box::new(1)
    } else {
        // returns string slice
        Box::new("one")
    }
}
fn main() {
    println!("Display: {}", get_dynamic_displayable(false));
}
```
Вывод
```
Display: one
```
## Сравнение реактивных самолетов 

```rust
use std::cmp::*;
use std::fmt;
struct Jet {
    name: String,
    velocity: f64, // km/h
}
// custom implementation of the Display trait
impl fmt::Display for Jet {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}, flying at {} km/h", self.name, self.velocity)
    }
}
// custom implementation of the PartialOrd trait
impl PartialOrd for Jet {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        self.velocity.partial_cmp(&other.velocity)
    }
}
// custom implementation of the PartialEq trait
impl PartialEq for Jet {
    fn eq(&self, other: &Self) -> bool {
        self.velocity == other.velocity
    }
}
fn main() {
    let g6 = Jet {
        name: String::from("Gulfstream G650"),
        velocity: 956.0,
    };
    let g7 = Jet {
        name: String::from("Gulfstream G700"),
        velocity: 937.0,
    };
    println!("G6 jet is {}", g6);
    println!("Does G6 velocity equal to G7's: {}", g6.eq(&g7));
    println!("Is G6 faster than G7: {}", g6.gt(&g7));
}
```
Вывод
```
G6 jet is Gulfstream G650, flying at 956 km/h
Does G6 velocity equal to G7's: false
Is G6 faster than G7: true
```
## Проверки заимствования - Borrow checker

```rust
// borrow checker
// compares scopes to determine whether all borrows are valid
// lifetime (how long it is alive) of variables annotated like " 'a "
// the lifetime of a variable is related to its scope
// Rust analyses the scope / lifetime of the values referenced by a variable
// using the borrow checker
fn main() {
    let fuel; // ========= START lifetime ('a) of fuel ==========
    {        
        let gasoil = String::from("gasoil"); // ===== START lifetime ('b) of gasoil =======
        fuel = &gasoil;
        println!("fuel is {}", fuel);
    } // ========= END lifetime of gasoil ================
} // ========= END lifetime of fuel ================
```
Программа проверки заимствований обнаружила ошибку: 
```rust
fn main() {
    let fuel; // ========= START 'a ================
    {
       
        let gasoil = String::from("gasoil"); // ===== START 'b =====
        fuel = &gasoil;
    } // ========= END 'b ================
    
    // fuel stores a borrowed reference to gasoil
    // gasoil lifetime is done
    // fuel contains a dangling reference, the program will not compile
    println!("fuel is {}", fuel);
} // ========= END 'a ================
```
Исправление ошибки срока службы путем увеличения срока службы gasoil: 
```rust
fn main() {
    let fuel; // ========= START 'a ================
    let gasoil = String::from("gasoil"); // ======= START 'b ========
    {
        fuel = &gasoil;
    } 
    
    println!("fuel is {}", fuel);
} // ========= END 'a and 'b ================
```
Вывод
```
fuel is gasoil
```
## Аннотация времени жизни
```rust
// defining a generic lifetime 'a
// explicitly defining a generic lifetime for parameters 
// is called lifetime annotation
// must begin with apostrophe ('x)
// convention is to use a single lowercase letter ('a, 'b, 'c)
// but you are free to name it whatever ('a_lifetime, 'toto)
// pay attention to the lifetime annotation
// which follows the borrow operator followed by a space then the type
// in this example, by annotating the return type,
// we tell the compiler that the returned value has the same lifetime as the params
// in case there are different lifetimes,
// the compiler will take the smallest
// by annotating the lifetime of the parameters,
// we give the borrow checker the info to validate the returned reference
fn get_longest_name<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
fn main() {
    let result;
    let name1 = String::from("Deep Space 9");
    let name2 = String::from("Voyager");
    result = get_longest_name(&name1, &name2);
    println!("result is {}", result);
}
```
Вывод
```
result is Deep Space 9
```
## Множественные аннотации времени жизни 
```rust
// notice here that although the return type has the same lifetime as one param
// we must annotate the other param with another lifetime
// even if never returned
// to avoid any confusion
// remember that here thhe code is simple
// but the input and the returned value could coming from a compiled library
// that the compiler does not have access to the source code
fn get_longest_name<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        x
    }
}
fn main() {
    let result;
    // START 'a
    let name1 = String::from("Deep Space 9");
    {
        // START 'b
        let name2 = String::from("Voyager");
        result = get_longest_name(&name1, &name2);
    } // END 'b
println!("result is {}", result);
} // END 'a
```
Вывод
```
result is Deep Space 9
```
## Правила исключения времени жизни
```rust
// lifetime elision rules
// set of rules for the compiler to analyze reference lifetimes
// describes situations that do not require explicit lifetime annotations
// if any ambiguity remains, explicit lifetime annotation will be required
// currently, 3 lifetime elision rules:
// #1
// each input parameter that is a reference is assigned its own lifetime
// fn get_first_word<'a>(s: &'a str) -> &str {
// lifetimes only apply for parameters that are references,
// non-reference parameters don't need them
// fn get_first_word<'a>(s: &'a str, t: i32) -> &str {
// fn get_first_word<'a, 'b>(s: &'a str, t: &'b str) -> &str {
// fn get_first_word<'a, 'b, 'c>(s: &'a str, t: &'b str, u: &'c str) -> &str {
// #2
// if there's only one input lifetime,
// assign it to all output lifetimes
// (the output lifetime must necessarily match one of the input lifetime)
// qualifies for elision thus lifetimes can be omitted:
// fn get_first_word<'a>(s: &'a str) -> &'a str {
// ==>``fn get_first_word(s: &str) -> &str {
// #3
// if there's a &self or &mut self input parameter,
// its lifetime will be assigned to all output lifetimes
// fn do_stuff(&self, input: &str) -> &str {
// rule #1 ==> fn do_stuff<'a,'b>(&'a self, input: &'b str) -> &str {
// rule #3 ==> fn do_stuff<'a,'b>(&'a self, input: &'b str) -> &'a str {
// these elision rules are for the compiler
// but it's good to know when you can omit lifetimes
fn main() {
    let message = String::from("Hi there");
    let first_word = get_first_word(&message);
    println!("first_word is {}", first_word);
}
fn get_first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (index, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..index]; // found blank space
        }
    }
    &s // no blank space found, input is a single word
}
```
Вывод
```
first_word is Hi
```
## Аннотации времени жизни структур 
```rust
struct Spaceship<'a> {
    // the struct has ownership of that string
    // when the struct goes out of scope,
    // that string data will be dropped and removed from the heap
    name: String,
    // the struct does not own the data referenced by this field
    // there is ambiguity if the struct can still use that reference
    // need to add explicit lifetime annotation
    nickname: &'a str,
}
// Annotate lifetimes to impl.
impl<'a, 'b> Spaceship<'a> {
    // because of lifetime elision rule #3,
    // no need for explicit lifetime annotation
    // the output lifetimes will have the lifetime as the &self parameter
    fn send_transmission(&self, msg: &str) -> (&str, &str) {
        println!("Transmitting message: {}", msg);
        (&self.name, self.nickname)
    }
    // here, because the output lifetime is different from the &self input lifetie,
    // need for explicit lifetime annotation
    fn test_send_transmission(&'a self, msg: &'b str) -> &'b str {
        println!("Transmitting message: {}", msg);
        msg
    }
}
fn main() {
    let saucer = Spaceship {
        name: String::from("TR95 MCC Enterprise 9"),
        nickname: "TR95",
    };
    let sender = saucer.send_transmission("All aboard!");
    println!("sender is {}", sender.1);
    let test_msg = saucer.test_send_transmission("this is a test");
    println!("test_msg is {}", test_msg);
}
```
Вывод
```
Transmitting message: All aboard!
sender is TR95
Transmitting message: this is a test
test_msg is this is a test
```
## Статическое время жизни 
```rust
// 'static lifetime
// indicates references available for the entire duration of the program
// example: a string literal is stored in the program binary
// so it never goes away
// the data is available from start to finish
// all string literals have a static lifetime:
//  let literal: &'static str = "I am a string slice";
// will never become invalid and lives longer than other lifetimes
// 'static lifetime as a trait bound
// ensures the data type will only contain 'static elements
// so that the receiver can hold onto it 
// and use it as long as they want knowing it will never become invalid:
// <T: Display + 'static>
fn get_toto<'a>() -> &'a str {
    "toto here"
}
fn main() {
  let toto: &'static str = get_toto();
  println!("toto is {}", toto);
}
```
Вывод
```
toto is toto here
```
## Подробнее о перечислениях 
```rust
#[derive(Debug)]
enum Shape {
    // storing data in enum variants by declaring parameter types
    Triangle(f64, f64, f64), // sides a, b, c
    Rectangle(f64, f64),     // width, height
    Circle(f64),             // radius
}
fn main() {
    let a_shape = Shape::Triangle(1.1, 2.2, 3.3);
    println!("a_shape is {:?}", a_shape);
    let a_shape = Shape::Rectangle(1.3, 5.7);
    println!("a_shape is {:?}", a_shape);
    let a_shape = Shape::Circle(1.13);
    println!("a_shape is {:?}", a_shape);
    // match operator
    // compares a value to a series of patterns 
    // to determine which code to execute
    // similar to switch statements in other languages
    // match expression used to control flow of program
    match a_shape {
        // enumerate all possible values
        // capture the stored data 
        // using a positional parameter name (a, b, c)
        // the logic to execute, when a match, is after the " => "
        Shape::Triangle(a, b, c) => println!(
            "a_shape is a triangle with sides a = {}, b = {}, c = {}",
            a, b, c
        ),
        Shape::Rectangle(width, height) => println!(
            "a_shape is a rectangle with width = {} and height = {}",
            width, height
        ),
        Shape::Circle(radius) => println!("a_shape is a circle with radius {}", radius),
    }
}
```
Вывод
```
a_shape is Triangle(1.1, 2.2, 3.3)
a_shape is Rectangle(1.3, 5.7)
a_shape is Circle(1.13)
a_shape is a circle with radius 1.13
```
## Соответствие шаблону подстановки 
```rust
fn main() {
    let number = 123u8;
    // match expression returning values based on case
    // match arms are evaluated sequentially from top to bottom
   let result: &str = match number {
        // match arm
        0 => "zero",
        // match arm
        1 => "one",
        // match arm
        2 => "two",
        // wildcard pattern (default case)
        // represented by an underscore symbol (_)
        // should be used last, 
        // otherwise the following arms will not be evaluated.
        // useful when you don't want to define all possible match arms explicitly 
        // (or simply because there is a high or infinite number of possibilities)
        // notice the code block when more than one line
        _ => {
            println!("{} did not match any arm", number);
            "unmatched"
        }
    };
    println!("result is {}", result);
}
```
Вывод
```
123 did not match any arm
result is unmatched
```
## Enum методы 
```rust
#[derive(Debug)]
enum Shape {
    Triangle(f64, f64, f64), // sides a, b, c
    Rectangle(f64, f64),     // width, height
    Circle(f64),             // radius
}
impl Shape {
    fn get_perimeter(&self) -> f64 {
        // no need to dereference the self variable
        // the compiler will automatically reference/dereference patterns in match expressions
        // match *self {
        match self {
            Shape::Rectangle(width, height) => (width + height) * 2.0,
            Shape::Triangle(a, b, c) => a + b + c,
            Shape::Circle(radius) => radius * 2.0 * std::f64::consts::PI,
        }
    }
}
fn main() {
    let a_shape = Shape::Triangle(1.1, 2.2, 3.3);
    println!("a_shape is {:?}", a_shape);
    println!("a_shape perimeter is {}", a_shape.get_perimeter());
    let a_shape = Shape::Rectangle(1.3, 5.7);
    println!("a_shape is {:?}", a_shape);
    println!("a_shape perimeter is {}", a_shape.get_perimeter());
    let a_shape = Shape::Circle(1.13);
    println!("a_shape is {:?}", a_shape);
    println!("a_shape perimeter is {:.2}", a_shape.get_perimeter());
}
```
Вывод
```
a_shape is Triangle(1.1, 2.2, 3.3)
a_shape perimeter is 6.6
a_shape is Rectangle(1.3, 5.7)
a_shape perimeter is 14
a_shape is Circle(1.13)
a_shape perimeter is 7.10
```
## Option<T> enum
```rust
// in many languages,
//  errors occur when using a null value in a not-null context
// Rust does not have a traditional null value
// instead Rust uses a generic enum named Option
// which can be of two variants:
// enum Option<T> {
//   Some(T),
//   None
// }
// 1) Some: indicates that has a value
// 2) None : indicates that no value
// the Option enum is included in the prelude
fn main() {
    // instantiate an option enum
    let someone: Option<i32> = Some(1);
    println!("someone is {:?}", someone);
    let something: Option<&str> = Some("thing");
    println!("something is {:?}", something);
    let nothing: Option<i32> = None;
    println!("nothing is {:?}", nothing);
    let countdown = [5, 4, 3, 2, 1];
    // use get() on slices to get an option enum holding
    // a reference to the value at the specified index
    let item = countdown.get(5);
    println!("item is {:?}", item);
    let item = countdown.get(0);
    println!("item is {:?}", item);
    let item = countdown.get(2);
    let item = item.unwrap_or(&0) + 1;
    println!("item is {:?}", item);
    let item = countdown.get(20);
    // unwrap_or(&default_value): 
    // if variant is Option::Some returns the stored data
    // if the variant is Option::None, the passed argument is used instead
    // notice that the argument is a reference
    let item = item.unwrap_or(&0) + 1;
    println!("item is {:?}", item);
}
```
Вывод
```
someone is Some(1)
something is Some("thing")
nothing is None
item is None
item is Some(5)
item is 4
item is 1
```
## Соответствие Option<T>

```rust
fn main() {
    let countdown = [5, 4, 3, 2, 1];
    let item = countdown.get(5);
    println!("item is {:?}", item);
    let item = countdown.get(0);
    println!("item is {:?}", item);
    let item = countdown.get(3);
    // using match expression to return value
    let item = match item {
        Some(value) => value + 1,
        None => 0,
    };
    println!("item is {:?}", item);
    let item = countdown.get(20);
    let item = match item {
        Some(value) => value + 1,
        None => 0,
    };
    println!("item is {:?}", item);
}
```
Вывод
```
item is None
item is Some(5)
item is 3
item is 0
```
## Соответствие if-let 
```rust
// if let syntax
// use when matching only one variant
fn main() {
    let number = Some(1);
    // notice the syntax:
    // if let PATTERN = VALUE {}
    if let Some(1) = number {
        println!("You are the one");
    }
}
```
Вывод
```
You are the one
```
## Location enum
```rust
use std::fmt;
enum Location {
    Unknown,
    Anonymous,
    Known(f64, f64), // latitude, longitude
}
impl fmt::Display for Location {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        match self {
            Location::Anonymous => write!(f, "Location is anonymous"),
            Location::Unknown => write!(f, "Location is unknown"),
            Location::Known(latitude, longitude) => {
                write!(f, "latitude: {} longitude: {}", latitude, longitude)
            }
        }
    }
}
fn main() {
    let address = Location::Anonymous;
    println!("address is {}", address);
    let address = Location::Unknown;
    println!("address is {}", address);
    let address = Location::Known(123.4, 456.7);
    println!("address is {}", address);
}
```
Вывод
```
address is Location is anonymous
address is Location is unknown
address is latitude: 123.4 longitude: 456.7
```
## Невосстановимые ошибки 
```rust
// two types of runtime errors in Rust
// Recoverable (ex: file not found error)
// Unrecoverable (ex: index out of array bounds)
// most languages do not distinguish between these errors
// and use Exceptions to handle all of them
// Rust does not have traditional Exceptions:
// Rust uses the Result<T, E> enum type for recoverable errors
// Rust uses panics for unrecoverable errors
fn main() {
    // panic! macro
    // immediately terminates the program 
    // and provides feedback to caller of program
    // panic!("Something went wrong.");
    let countdown: [i32; 4] = [3, 2, 1, 0];
    for left in countdown.iter() {
        println!("T-minus {}", left);
        // the program will panic when diving by zero
        let q = 1 / left;
        println!("q is {}", q);
    }
}
```
Вывод
```
T-minus 3
q is 0
T-minus 2
q is 0
T-minus 1
q is 1
T-minus 0
thread 'main' panicked at 'attempt to divide by zero', /home/<USER>/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/library/core/src/ops/arith.rs:474:1
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

## Восстановимые ошибки
```rust
// recoverable errors
// errors that do not cause the program to fail
// and can be corrected
// Rust uses the Result<T,E> enum type to handle recoverable errors
// enum Result<T, E> {
//     Ok(T),
//     Err(E),
// }
// the Ok variant stores the value of the successful operation
// the Err variant stores the value of the error
// the Result enum is included in the prelude
use std::fs;
use std::io;
fn main() {
    // read_to_string() returns a Result<T, E> enum
    // create a file named file.txt in the root of the project folder:
    // $ echo 42 > file.txt
    let content: Result<String, io::Error> = fs::read_to_string("file.txt");
    // avoid the unwrap() method
    // because will panic if this is the Err variant of the Result enum
    println!("content is {:?}", content.unwrap());
    // for custom error message, use expect()
    // not the best way to handle recoverable errors
    let content: Result<String, io::Error> = fs::read_to_string("file-x.txt");
    println!("content is {:?}", content.expect("failed reading file"));
}
```
Вывод
```
content is "42\n"
thread 'main' panicked at 'failed reading file: Os { code: 2, kind: NotFound, message: "No such file or directory" }', src/main.rs:35:41
```

## Matching Result<T, E>enum для обработки ошибок 

```rust
use std::fs;
use std::io;
fn main() {
    let result = fs::read_to_string("does-not-exist.txt");
    let content = match result {
        Ok(text) => text,
        // handle I/O error based on kind
        Err(error) => match error.kind() {
            io::ErrorKind::NotFound => String::from("File not found"),
            io::ErrorKind::PermissionDenied => {
              String::from("Permission denied")
            },
            _ => error.to_string(),
        },
    };
    println!("content is \"{}\"", content);
}
```
Вывод
```
content is "File not found"
```
## Распространение ошибок 
```rust
use std::fs;
use std::io;
fn read_and_combine(f1: &str, f2: &str) -> Result<String, io::Error> {
    let mut s1 = match fs::read_to_string(f1) {
        Ok(s) => s,
        // propagating the error to caller
        Err(e) => return Err(e),
    };
    // Rust provides a shorthand syntax for propagating errors
    // by removing the match expression
    // and putting a question mark (?) after the Result enum
    // it is equivalent to the above syntax
    // the ? can only be used with functions that return a Result enum
    let s2 = fs::read_to_string(f2)?;
    s1.push('\n');
    s1.push_str(&s2);
    Ok(s1)
}
fn main() {
    match read_and_combine("file1.txt", "file2.txt") {
        Ok(text_result) => println!("result is:\n{}", text_result),
        Err(e) => println!("Got error: {}", e),
    };
}
```
Вывод
```
Got error: No such file or directory (os error 2)
```
## Возвращение к игре в угадывание с обработкой ошибок 
```rust
use rand::prelude::*;
use std::io;
fn main() {
    guess_with_infinite_retry();
}
fn guess_with_infinite_retry() {
    let secret_number = thread_rng().gen_range(1..101);
    let mut invalid_numberretry_limit = 0;
    println!("Guess a number between 1 and 100:");
    loop {
        let mut buffer = String::new();
        let guess = match io::stdin().read_line(&mut buffer) {
            Ok(_) => match buffer.trim().parse::<u32>() {
                Ok(number) => number,
                Err(e) => {
                    invalid_numberretry_limit += 1;
                    if invalid_numberretry_limit == 3 {
                        panic!("Too many invalid numbers entered");
                    }
                    println!("Failed to parse guess: {}", e);
                    println!("Please enter a valid number:");
                    continue;
                }
            },
            Err(e) => {
                println!("Failed to read line: {}", e);
                continue;
            }
        };
        if guess < secret_number {
            println!("guess too low, guess higher:");
        } else if guess > secret_number {
            println!("guess too high, guess lower:");
        } else {
            println!("you found it!");
            break;
        }
    }
}
```
Вывод
```
Guess a number between 1 and 100:
Failed to parse guess: cannot parse integer from empty string
Please enter a valid number:
Failed to parse guess: cannot parse integer from empty string
Please enter a valid number:
thread 'main' panicked at 'Too many invalid numbers entered', src/main.rs:19:25
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```
Или
```
Guess a number between 1 and 100:
44
guess too low, guess higher:
77
guess too low, guess higher:
99
guess too high, guess lower:
88
guess too high, guess lower:
80
you found it!
```
## Векторы с обработкой ошибок 
```rust
fn main() {
    // create an empty vector
   let mut presidents: Vec<String> = Vec::new();
    presidents.push(String::from("Chirac"));
    presidents.push(String::from("Sarkozy"));
    presidents.push(String::from("Hollande"));
    presidents.push(String::from("Macron"));
   println!("presidents are {:?}", presidents);
    let last = match presidents.pop() {
        Some(president) => president,
        None => "not found".to_string(),
    };
    println!("last is {}", last);
    let fourth = match presidents.get(3) {
        Some(president) => president,
        None => "not found",
    };
    println!("fourth president is {}", fourth);
    // creating a vector with the vec![] macro
    let countdown = vec![4, 3, 2, 1];
    println!("countdown is {:?}", countdown);
}
```
Вывод
```
presidents are ["Chirac", "Sarkozy", "Hollande", "Macron"]
last is Macron
fourth president is not found
countdown is [4, 3, 2, 1]
```
## Тип данных HashMap<K, V>

```rust
// stores data in key / value pairs
// use the keys to lookup corresponding values
// key -> value mapping is one way
// Rust uses a hash function to determine how to store data
// keys and values can be different data types
// all keys must have the same type
// all values must have the same type
// each key can only have one value associated with it at a time
// you need to import the type
// updating hashmap entries, approaches:
// #1: overwrite existing key/value pair
// #2: insert a new entry if a key does not exist
// #3: modify a value based on its existing value
use std::collections::HashMap;
fn main() {
    let mut missions = HashMap::new();
    missions.insert("Toto", 23); // HashMap<&str, i32>
    missions.insert("Toto", 33); // #1 update
    missions.insert("Titi", 45);
    missions.insert("Tata", 67);
    missions.entry("Tutu").or_insert(77); // #2 update
    // #3 update
    let tyty = missions.entry("Titi").or_insert(0);
    *tyty += 1;
    println!("missions is {:?}", missions.clone());
    let toto_missions = match missions.get("Toto") {
        Some(value) => value,
        None => &0,
    };
    println!("Toto_missions is {}", toto_missions);
}
```
Вывод
```
missions is {"Tata": 67, "Titi": 46, "Toto": 33, "Tutu": 77}
Toto_missions is 33
```
## Счетчик слов

Создайте файл с именем «lorem.txt» со следующим содержимым: 

```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis porta velit quis mattis bibendum. Fusce sed nulla eget arcu lacinia placerat ac eget dui. Nam a ante tellus. Aliquam varius tincidunt nisi a efficitur. Donec non malesuada odio. Donec vitae arcu eu magna efficitur pulvinar. Proin feugiat, erat et congue aliquet, ipsum turpis volutpat ante, a ullamcorper magna elit nec libero. Maecenas lacus magna, gravida id tristique at, lobortis sit amet libero.
Donec vel nunc ac sapien tempus vehicula. Aenean vulputate quam eget felis elementum suscipit. Aliquam vehicula odio dui, id varius lectus gravida consequat. Maecenas sit amet dolor erat. Duis rhoncus mollis nulla. Etiam pellentesque arcu sed pretium fermentum. Phasellus metus velit, dapibus eu pellentesque eu, pulvinar eu nunc. In posuere massa non elementum varius. Donec et vehicula urna, id dignissim ex.
Nunc vitae sem volutpat, malesuada odio vel, ornare arcu. Nunc dictum tincidunt turpis, sed tristique tellus hendrerit at. In ac tempor lacus. Pellentesque tempus velit eu pharetra consectetur. Integer ultricies sem sem, a tincidunt urna tempus quis. Nullam porttitor turpis ut lacus mollis dignissim nec vel ipsum. Integer volutpat quam et enim pellentesque, ut imperdiet nibh faucibus. Vestibulum varius, libero eget porta congue, enim risus porttitor lacus, at pretium mauris ligula sit amet ligula.
Donec at pharetra elit. Sed nulla dui, consectetur sollicitudin magna a, consequat maximus erat. Maecenas ultricies libero orci, a cursus justo blandit non. Vivamus non fringilla ante. In cursus finibus elit quis cursus. Maecenas at arcu id ex consectetur eleifend. Aenean nec purus eget odio commodo ullamcorper. Integer maximus quis turpis id finibus. Etiam mi nunc, fringilla eget diam non, gravida blandit nisl. Curabitur finibus bibendum consequat. Nunc eleifend aliquam risus a iaculis.
Nullam id lacus sem. Suspendisse a nunc facilisis, rutrum purus aliquet, pulvinar libero. Etiam ornare, enim at egestas varius, mi justo feugiat purus, eget congue nisi nibh vitae mauris. Integer malesuada, diam et placerat mollis, purus nulla molestie lacus, in egestas nisl lacus et odio. Fusce feugiat odio eget justo lacinia placerat. In rutrum et quam sed gravida. Vivamus vel blandit metus, sed imperdiet turpis. Sed facilisis ipsum ipsum, egestas mattis dolor tempus at. Morbi eu scelerisque ante.
```

Исходный код 

```rust
use std::collections::HashMap;
use std::env;
use std::fmt;
use std::fs;
#[derive(Debug)]
struct MostCommonWord {
    value: String,
    occurrence: u32,
}
impl fmt::Display for MostCommonWord {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(
            f,
            "\"{}\" is the most common word with {} occurrences",
            self.value, self.occurrence
        )
    }
}
impl MostCommonWord {
    fn new(value: &str, occurrence: u32) -> MostCommonWord {
        MostCommonWord {
            value: value.to_string(),
            occurrence,
        }
    }
}
fn main() {
    if env::args().len() != 2 {
        println!("this program requires one argument");
        return;
    }
    let filepath = match env::args().nth(1) {
        Some(text) => text,
        None => panic!("Failed to get file path"),
    };
    // println!("filepath is {}", filepath);
    let file_content = match fs::read_to_string(filepath) {
        Ok(content) => content,
        Err(e) => panic!("Err: {}", e),
    };
    // println!("file_content is:\n {}", file_content);
    let mut word_count: HashMap<String, u32> = HashMap::new();
    for word in file_content.split_whitespace() {
        let lowercase_word = word.to_lowercase();
        match word_count.get(&lowercase_word) {
            Some(count) => {
                let increment = *count + 1;
                word_count.insert(lowercase_word, increment);
            }
            None => {
                word_count.insert(lowercase_word, 1);
            }
        };
    }
    let mut most_common = MostCommonWord::new("", 0);
    let mut most_common_words = vec![];
    for (word, count) in &word_count {
        if most_common.occurrence < *count {
            most_common.value = word.to_string();
            most_common.occurrence = *count;
        }
    }
    most_common_words.push(most_common);
    for (word, count) in &word_count {
        if most_common_words[0].value != word.to_string()
            && most_common_words[0].occurrence == *count
        {
            let other_most_common = MostCommonWord::new(word, *count);
            most_common_words.push(other_most_common);
        }
    }
    if most_common_words.len() > 1 {
        println!("The most common words in the text are;");
        for most_common in most_common_words {
            println!(
                "\"{}\" with {} occurrences",
                most_common.value, most_common.occurrence
            );
        }
    } else if most_common_words.len() == 1 {
        println!("{}", most_common_words[0]);
    }
}
```
Вывод
```
$ cargo run lorem.txt
"eget" is the most common word with 8 occurrences
```

## Альтернативная реализация 

```rust
use std::env;
use std::fs;
use std::collections::HashMap;
fn main() {
    // read file and build vector of individual words
    let contents = match env::args().nth(1) {
        Some(f) => match fs::read_to_string(f) {
            Ok(s) => s.to_lowercase(),
            Err(e) => {
                eprintln!("Could not read file: {}", e);
                std::process::exit(1);
            }
        },
        None => {
            eprintln!("Program requires an argument: <file path>");
            std::process::exit(2);
        }
    };
    let all_words = contents.split_whitespace().collect::<Vec<&str>>();
    // count how many times each unique word occurs
    let mut word_counts: HashMap<&str, u32> = HashMap::new();
    for word in all_words.iter() {
        *word_counts.entry(word).or_insert(1) += 1;
    }
    
    // determine the most commonly used word(s)
    let mut top_count = 0u32;
    let mut top_words: Vec<&str> = Vec::new();
    for (&key, &val) in word_counts.iter() {
        if val > top_count {
            top_count = val;
            top_words.clear();
            top_words.push(key);
        } else if val == top_count {
            top_words.push(key);
        }
    }
    // display results
    println!("Top word(s) occurred {} times:", top_count);
    for word in top_words.iter() {
        println!("{}", word);
    }
}
```