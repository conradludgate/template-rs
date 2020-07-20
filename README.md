# template-rs
Powerful Rust templating library

## Example

Inline templating
```rust
let title = "Title";

let n = 4;

let output: String = template!(
<html>
    <head>
        <title>#title</title>
    </head>
    <body>
        #{
            if n % 2 == 0 {
                format!("{} is even", n)
            } else {
                format!("{} is even", n)
            }
        }
    </body>
</html>
);

assert_eq!(output, "
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        4 is even
    </body>
</html>
".to_string());
```

Precompiled/reusable templates
```rust
struct Data {
    pub title: &'static str,
    pub n: isize,
}

// impl Template for Data { fn compile(self) -> String { ... } }
template!(Data =>
<html>
    <head>
        <title>#title</title>
    </head>
    <body>
        #{
            if n % 2 == 0 {
                format!("{} is even", n)
            } else {
                format!("{} is even", n)
            }
        }
    </body>
</html>
);

let output: String = Data{title: "My page", n: 3}.compile();

assert_eq!(output, "
<html>
    <head>
        <title>My page</title>
    </head>
    <body>
        3 is odd
    </body>
</html>
".to_string());
```

If you want to use the same type for multiple templates, use
```rust
template!(Data as Document =>
<html>
    <head>
        <title>#title</title>
    </head>
    <body>
        #{
            if n % 2 == 0 {
                format!("{} is even", n)
            } else {
                format!("{} is even", n)
            }
        }
    </body>
</html>
);

let output: String = Document(Data{title: "My page", n: 3}).compile();
```

## How

The template macro accepts any data.
If there is a `#`, the next `ident` or `group` token is taken as a variable for the template.
If the token is an ident, then the type should implement `TryInto<TemplateValue>`.
If the token is a group, then it should resolve to a single expression which also implements `TryInto<TemplateValue>`.

