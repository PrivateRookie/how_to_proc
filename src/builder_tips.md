# Builder

## 解析 Option 类型
> Be aware that the Rust compiler performs name resolution only after macro
> expansion has finished completely. That means during the evaluation of a
> procedural macro, "types" do not exist yet, only tokens. 

在过程宏中不存在“类型”，只有 token, 因此存在以下可能，不同 token 表示同一种类型，如
`Option<T>` 和 `std::option::Option<T>` 其实是同一种类型；反过来说，单个 token 可能指向不同类型，如 `Error` 在 `use std::error::Error` 和 `use <some_crate>::Error` 作用指向类型就不一致。

---

## 惰性属性

某些情况下，derive macro 还需要“修饰”某些字段，如下面的 `#[builder](...)`

```rust
#[derive(Builder)]
pub struct Command {
    executable: String,
    #[builder(each = "arg")]
    args: Vec<String>
}
```

这些属性被称为 **inert attributes**, 即惰性属性，寓意他们不独立存在，而是被其他宏触发。

在定义 derive macro 时可以传入额外参数指定该 derive macro 对应的惰性属性

```rust
#[proc_macro_derive(Builder, attributes(builder))]
pub fn impl_macro...
```

---

## 报告错误

> The preferred way to report an error from a procedural macro is by > luding
> an invocation of the standard library's compile_error macro in the code
> emitted by the procedural macro.

建议使用 `compile_error!` 发送清晰的错误信息

TODO 添加更多例子

---

## 使用绝对路径

回到上面提到的 `Error` 在不同作用域含义不同的例子，为了避免这种情况方式，宏(过程宏和范例宏)都应该使用绝对路径，如 `std::result::Result`

