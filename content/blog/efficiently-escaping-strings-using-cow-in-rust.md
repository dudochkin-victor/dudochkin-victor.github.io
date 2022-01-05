+++
title = "Эффективное экранирование строк с помощью Cow in Rust"
description = "Эффективное экранирование строк с помощью Cow in Rust"
weight = 1
+++

[Перевод](https://fullstackmilk.dev/efficiently_escaping_strings_using_cow_in_rust/) | Автор оригинала: Full stack milk

Это удобный шаблон для эффективного экранирования текста, а также хорошая демонстрация типа Cow в Rust.

По сути, если нам нужно экранировать некоторый текст (для включения в HTML или CSV-файл или что-то еще), нам может потребоваться вернуть новую строку, в которой все специальные символы в оригинале заменены соответствующими escape-последовательностями. Но, вероятно, большую часть наших входных строк на самом деле не нужно экранировать, так как же нам избежать ненужного выделения группы строк в таких случаях?

Если строка требует экранирования, мы хотим вернуть новую строку, но если нет, мы хотим вернуть ссылку на исходную (&str) и полностью избежать выделения памяти. std::заимствовать::Cow пригодится для этого. Это просто простое перечисление либо некоторых заимствованных данных (в нашем случае &str), либо собственного эквивалента этих данных (String).

Суть нашего алгоритма такова:

- Перебирайте символы в строке, пока не найдете тот, который нужно экранировать.
- Если вы не нашли никаких специальных символов, просто верните уже существующую строку с помощью Cow::Borrowed (ввод) (новые выделения не требуются 🎉).
- Если вы найдете особого персонажа, то:
    - Скопируйте символы до этого момента в новую изменяемую строку (мы уже знаем, что символы, стоящие перед первым специальным символом, безопасны, потому что мы уже проверили их).
    - Перебираем оставшиеся символы, экранируя их по мере необходимости, а затем добавляя их в нашу новую строку.
    - Верните новую строку, используя Cow::Owned (escaped_string).

Вот пример экранирования символов HTML: 

```rust
use std::borrow::Cow;

pub fn html_escape(input: &str) -> Cow<str> {
    // Iterate through the characters, checking if each one needs escaping
    for (i, ch) in input.chars().enumerate() {
        if html_escape_char(ch).is_some() {
            // At least one char needs escaping, so we need to return a brand
            // new `String` rather than the original

            let mut escaped_string = String::with_capacity(input.len());
            // Calling `String::with_capacity()` instead of `String::new()` is
            // a slight optimisation to reduce the number of allocations we
            // need to do.
            //
            // We know that the escaped string is always at least as long as
            // the unescaped version so we can preallocate at least that much
            // space.

            // We already checked the characters up to index `i` don't need
            // escaping so we can just copy them straight in
            escaped_string.push_str(&input[..i]);

            // Escape the remaining characters if they need it and add them to
            // our escaped string
            for ch in input[i..].chars() {
                match html_escape_char(ch) {
                    Some(escaped_char) => escaped_string.push_str(escaped_char),
                    None => escaped_string.push(ch),
                };
            }

            return Cow::Owned(escaped_string);
        }
    }

    // We've iterated through all of `input` and didn't find any special
    // characters, so it's safe to just return the original string
    Cow::Borrowed(input)
}

fn html_escape_char(ch: char) -> Option<&'static str> {
    match ch {
        '&' => Some("&amp"),
        '<' => Some("&lt;"),
        '>' => Some("&gt;"),
        '"' => Some("&quot;"),
        '\'' => Some("&#x27;"),
        _ => None,
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn returns_text_that_does_not_need_escaping_as_is() {
        let input = "This is a safe string!";

        let escaped = html_escape(input);

        assert_eq!(escaped, Cow::Borrowed(input));
    }

    #[test]
    fn escapes_text_containing_html_special_characters() {
        let input = "This is a <script>alert('nasty');</script> string";

        let expected: Cow<str> = Cow::Owned(
            "This is a &lt;script&gt;alert(&#x27;nasty&#x27;);&lt;/script&gt; string".to_string(),
        );

        let escaped = html_escape(input);

        assert_eq!(escaped, expected);
    }
}
```
